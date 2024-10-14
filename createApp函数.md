
源码文件所在位置： core-main\packages\runtime-dom\src\apiCreateApp.ts

```
export function createAppAPI<HostElement>(
  render: RootRenderFunction<HostElement>,
  hydrate?: RootHydrateFunction,
): CreateAppFunction<HostElement> {
    return function createApp(rootComponent, rootProps = null) {
        // 省略代码，主要是返回了一个 App对象
    }
}
```

### App对象主要拥有以下属性：
- version vue版本号（3.5.4）
- config 全局配置
- use 函数
- mixin 混入（vue3已经不推荐使用了，主要是为了兼容vue2）
- component 组件注册函数
- directive  指令注册函数
- mount 实例挂载
- unmount 卸载已挂载的实例
- onUnmount 注册一个回调函数，在应用卸载时调用
- provide 依赖提供函数（可以在应用中的所有后代组件中注入使用）
- runWithContext 使用当前应用作为注入上下文执行回调函数



App 对象代码：

```
export interface App<HostElement = any> {
  version: string
  config: AppConfig
  use<Options extends unknown[]>(
    plugin: Plugin<Options>,
    ...options: Options
  ): this
  use<Options>(plugin: Plugin<Options>, options: Options): this
  mixin(mixin: ComponentOptions): this
  component(name: string): Component | undefined
  component<T extends Component | DefineComponent>(
    name: string,
    component: T,
  ): this
  directive<
    HostElement = any,
    Value = any,
    Modifiers extends string = string,
    Arg extends string = string,
  >(
    name: string,
  ): Directive<HostElement, Value, Modifiers, Arg> | undefined
  directive<
    HostElement = any,
    Value = any,
    Modifiers extends string = string,
    Arg extends string = string,
  >(
    name: string,
    directive: Directive<HostElement, Value, Modifiers, Arg>,
  ): this
  mount(
    rootContainer: HostElement | string,
    vnode?: VNode,
  ): ComponentPublicInstance
  unmount(): void
  onUnmount(cb: () => void): void
  provide<T, K = InjectionKey<T> | string | number>(
    key: K,
    value: K extends InjectionKey<infer V> ? V : T,
  ): this

  runWithContext<T>(fn: () => T): T
  _uid: number
  _component: ConcreteComponent
  _props: Data | null
  _container: HostElement | null
  _context: AppContext
  _instance: ComponentInternalInstance | null
  _ceVNode?: VNode
  filter?(name: string): Function | undefined
  filter?(name: string, filter: Function): this
  _createRoot?(options: ComponentOptions): ComponentPublicInstance
}
```

接下来主要看看 [mount](mount) 函数
