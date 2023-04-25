---
layout: post
title: "react-路由缓存组件"
subtitle: "react也有keep-alive"
date: 2023-04-23 23:01:00
author: "PhcPro"
catalog: false
header-style: text
tags:
  - react
  - 组件缓存
  - ahooks
  - react-router
  - Map
  - 红黑树
--- 

## 数据结构关键：readyRenderNodes
```ts
const [readyRenderNodes, setReadyRenderNodes] = useState<
  Map<string, {
    children: RouteProps['children'],
    resolve: Function,
    uniqueId: string,
    okDom?: any
  }>
>(new Map())
```

## 算法关键: keeyAlive
```typescript
const KeepAlive = useMemoizedFn((path: string, children: React.ReactNode) => {
  const contextPath = path + '-' + pathSuffix
  return new Promise<HTMLDivElement>((resolve) => {
    if (readyRenderNodes.get(contextPath)) {
      resolve(readyRenderNodes.get(contextPath).okDom)
    } else {
      readyRenderNodes.set(contextPath, {
        children,
        uniqueId: contextPath,
        resolve
      })
      setReadyRenderNodes(new Map(readyRenderNodes))
    }
  })
})
```

## react相关：provider/consumer
```ts
cosnt { Provider, Consumer } = createContext<KeepAliveFn>(() => Promise.resolve())
```

## 类型声明

### KeepAliveFn
```ts
type KeepAliveFn = (path: string, children: RouteProps['children']) =>Promise<HTMLDivElement | void>
```

### RouteProviderProps
```ts
interface RouteProviderProps {
  children?: React.ReactNode
}
```

### RouteProps
```ts
interface RouteProps {
  uniqueId: string
  children?: React.ReactNode
}
```

## 导出

### RouteProvider
```ts
export const RouteProvider = (props: RouteProviderProps) => {
  const { children } = props
  const history = useHistory()
  const [pathSuffix, { inc }] = useCounter()

  // 数据结构关键：readyRenderNodes

  // 算法关键: keeyAlive

  // 登录页面时，清空所有缓存
  useMount(() => {
    history.listen((router) => {
      if (router.pathname === '/signin') {
        inc()
      }
    })
  })

  return (
    <Provider>
      {children}
      {Array.from(readyRenderNodes.values()).map(item => {
        return (
          <div key={item.uniqueId} ref={(ref) => {
            item.resolve(node)
            item.okDom = node
          }}>
            {item.children}
          </div>
        )
      })}
    </Provider>
  )
}
```

### Route
```ts
export const Route = (props: RouteProps) => {
  const { uniqueId, children } = props

  const onDomOk = async (keepAlive: KeepAliveFn, dom: HTMLDivElement) => {
    if (!dom) return
    const html = await keepAlive(uniqueId, children)
    dom.appendChild(html as HTMLDivElement)
  }

  return (
    // 没看懂，为什么keepalive并没有被定义
    <Consumer>
      {(keepAlive) => <div ref={(dom) => onDomOk(keepAlive, dom)}></div>}
    </Consumer>
  )
}
```