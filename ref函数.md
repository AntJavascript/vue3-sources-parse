源码文件所在位置：
core-main\packages\reactivity\src\ref.ts

### 1、首先看到调用了createRef函数
```
export function ref(value?: unknown) {
  return createRef(value, false)
}
```
### 2、createRef函数代码如下：
```
function createRef(rawValue: unknown, shallow: boolean) {
  if (isRef(rawValue)) {
    return rawValue
  }
  return new RefImpl(rawValue, shallow)
}
```
  ①、如果是isRef对象，直接返回该对象 **（所以我们平时把ref对象再用ref嵌套一层，其实就是对前一个ref对象的一个引用）**

  ②、实例化了 RefImpl 类

### 3、RefImpl类代码如下：
```
/**
 * @internal
 */
class RefImpl<T = any> {
  _value: T
  private _rawValue: T

  dep: Dep = new Dep()

  public readonly [ReactiveFlags.IS_REF] = true
  public readonly [ReactiveFlags.IS_SHALLOW]: boolean = false

  constructor(value: T, isShallow: boolean) {
    this._rawValue = isShallow ? value : toRaw(value)
    this._value = isShallow ? value : toReactive(value)
    this[ReactiveFlags.IS_SHALLOW] = isShallow
  }

  get value() {
    if (__DEV__) {
      this.dep.track({
        target: this,
        type: TrackOpTypes.GET,
        key: 'value',
      })
    } else {
      this.dep.track()
    }
    return this._value
  }

  set value(newValue) {
    const oldValue = this._rawValue
    const useDirectValue =
      this[ReactiveFlags.IS_SHALLOW] ||
      isShallow(newValue) ||
      isReadonly(newValue)
    newValue = useDirectValue ? newValue : toRaw(newValue)
    if (hasChanged(newValue, oldValue)) {
      this._rawValue = newValue
      this._value = useDirectValue ? newValue : toReactive(newValue)
      if (__DEV__) {
        this.dep.trigger({
          target: this,
          type: TriggerOpTypes.SET,
          key: 'value',
          newValue,
          oldValue,
        })
      } else {
        this.dep.trigger()
      }
    }
  }
}
```
① dep: Dep = new Dep() 定义了一个dep属性，<b style='color:red'>该属性极其重要，用于后续data、vNode、dom之间的联系，具体请看<a href=''>dep函数</a></b>

②  constructor 函数 isShallow(浅监听标识，默认false)，调用toReactive函数，如果 value 是一个对象，则使用reactive()函数监听, 

官方文档有说明 https://cn.vuejs.org/api/reactivity-core#ref
> 如果将一个对象赋值给 ref，那么这个对象将通过 reactive() 转为具有深层次响应式的对象。这也意味着如果对象中包含了嵌套的 ref，它们将被深层地解包。
>若要避免这种深层次的转换，请使用 shallowRef() 来替代。

toReactive代码如下：
```
export const toReactive = <T extends unknown>(value: T): T => isObject(value) ? reactive(value) : value
```
对于 reactive(value) 函数，具体请看<a href=''>reactive函数</a>

③ get 函数

- this.dep.track() 调用dep的track函数，追踪一个依赖值，便于后续更新 具体请看<a href=''>dep函数</a></b>
- return this._value 返回其值

④ set 函数
- 判断当前值是否为ref对象或者只读对象, 
- 赋值新值 newValue = useDirectValue ? newValue : toRaw(newValue)
- 对比新旧值是否有改变 hasChanged(newValue, oldValue)
