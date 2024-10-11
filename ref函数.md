文件所在位置：
core-main\packages\reactivity\src\ref.ts

##### 首先看到调用了createRef函数
```
export function ref(value?: unknown) {
  return createRef(value, false)
}
```
##### createRef函数代码如下：

1、如果是isRef对象，直接返回该对象 **（所以我们平时把ref对象再用ref嵌套一层，其实就是对前一个ref对象的一个引用）**

2、实例化了 RefImpl 类
```
function createRef(rawValue: unknown, shallow: boolean) {
  if (isRef(rawValue)) {
    return rawValue
  }
  return new RefImpl(rawValue, shallow)
}
```
