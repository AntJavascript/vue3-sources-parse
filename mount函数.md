源码文件所在位置： core-main\packages\runtime-dom\src\apiCreateApp.ts

```
 mount(
        rootContainer: HostElement,
        isHydrate?: boolean,
        namespace?: boolean | ElementNamespace,
      ): any {
        if (!isMounted) {
          // #5571
          if (__DEV__ && (rootContainer as any).__vue_app__) {
            warn(
              `There is already an app instance mounted on the host container.\n` +
                ` If you want to mount another app on the same host container,` +
                ` you need to unmount the previous app by calling \`app.unmount()\` first.`,
            )
          }
          const vnode = app._ceVNode || createVNode(rootComponent, rootProps)
          // store app context on the root VNode.
          // this will be set on the root instance on initial mount.
          vnode.appContext = context

          if (namespace === true) {
            namespace = 'svg'
          } else if (namespace === false) {
            namespace = undefined
          }

          // HMR root reload
          if (__DEV__) {
            context.reload = () => {
              // casting to ElementNamespace because TS doesn't guarantee type narrowing
              // over function boundaries
              render(
                cloneVNode(vnode),
                rootContainer,
                namespace as ElementNamespace,
              )
            }
          }

          if (isHydrate && hydrate) {
            hydrate(vnode as VNode<Node, Element>, rootContainer as any)
          } else {
            render(vnode, rootContainer, namespace)
          }
          isMounted = true
          app._container = rootContainer
          // for devtools and telemetry
          ;(rootContainer as any).__vue_app__ = app

          if (__DEV__ || __FEATURE_PROD_DEVTOOLS__) {
            app._instance = vnode.component
            devtoolsInitApp(app, version)
          }

          return getComponentPublicInstance(vnode.component!)
        } else if (__DEV__) {
          warn(
            `App has already been mounted.\n` +
              `If you want to remount the same app, move your app creation logic ` +
              `into a factory function and create fresh app instances for each ` +
              `mount - e.g. \`const createMyApp = () => createApp(App)\``,
          )
        }
      },
```
1、首先看看是否已经挂载 "isMounted"，如果已经挂载将会抛出一个 'warn' 警告
```
if (!isMounted) {
    // 省略代码

} else if (true) {
          warn$1(
            `App has already been mounted.
If you want to remount the same app, move your app creation logic into a factory function and create fresh app instances for each mount - e.g. \`const createMyApp = () => createApp(App)\``
          );
        }
```
2、当前挂载容器是否已经存在App实例（__vue_app__属性），如果已经存在将会抛出一个 'warn' 警告
```
if (__DEV__ && (rootContainer as any).__vue_app__) {
            warn(
              `There is already an app instance mounted on the host container.\n` +
                ` If you want to mount another app on the same host container,` +
                ` you need to unmount the previous app by calling \`app.unmount()\` first.`,
            )
          }
```
3、调用创建虚拟dom函数 "createVNode", 关于虚拟dom相关，请看 [VNode函数](VNode函数)
```
const vnode = app._ceVNode || createVNode(rootComponent, rootProps)
```
4、在实例对象上面注册了一个重新渲染函数（reload）,接下来就是调用render渲染界面， 关于render相关，请看 [render函数](render函数)
```
if (true) {
    context.reload = () => {
        render2(
        cloneVNode(vnode),
        rootContainer,
        namespace
        );
    };
}
if (isHydrate && hydrate2) {
    hydrate2(vnode, rootContainer);
    } else {
    render2(vnode, rootContainer, namespace);
}
```
5、设置挂载标识（isMounted）、实例标识（__vue_app__）
```
isMounted = true
app._container = rootContainer
;(rootContainer as any).__vue_app__ = app
```
6、初始化 devtools 调试工具
```
if (__DEV__ || __FEATURE_PROD_DEVTOOLS__) {
    app._instance = vnode.component
    devtoolsInitApp(app, version)
}
```
7、最后返回组件实例（一个Proxy对象）
```
return getComponentPublicInstance(vnode.component!)
```
