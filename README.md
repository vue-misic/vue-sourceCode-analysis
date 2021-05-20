# vue-sourceCode-analysis
vue-sourceCode-analysis
##
针对vue源码做深度分析，每一行代码的构建，达到深入理解的地步

## 响应式原理 Object.defineproperty()
Observer : 它的作用是给对象的属性添加 getter 和 setter，用于依赖收集和派发更新 defineReactive()
Dep : 用于收集当前响应式对象的依赖关系,每个响应式对象包括子对象都拥有一个 Dep 实例（里面 subs 是 Watcher 实例数组）,当数据有变更时,会通过 dep.notify()通知各个 watcher。
Watcher : 观察者对象 ,
实例分为渲染 watcher (render watcher)
计算属性 watcher (computed watcher)
侦听器 watcher（user watcher）三种

Watcher 和 Dep 的关系
watcher 中实例化了 dep 并向 dep.subs 中添加了订阅者,dep 通过 notify 遍历了 dep.subs 通知每个 watcher 更新。

依赖收集
顺序
    initProps 添加响应式/代理 this._propsKey
    initMethods 遍历去重与prop属性名是否重复
    initData 添加响应式/代理访问
    initComputed 添加computed Watcher    watcher.dirty = true
    initWatcher 添加watcher
initState 时,对 computed 属性初始化时,触发 computed watcher 依赖收集
initState 时,对侦听属性初始化时,触发 user watcher 依赖收集
render()的过程,触发 render watcher 依赖收集
re-render 时,vm.render()再次执行,会移除所有 subs 中的 watcer 的订阅,重新赋值。
派发更新
组件中对响应的数据进行了修改,触发 setter 的逻辑
调用 dep.notify()
遍历所有的 subs（Watcher 实例）,调用每一个 watcher 的 update 方法。


vue.js 采用数据劫持结合发布-订阅模式,通过 Object.defineproperty 来劫持各个属性的 setter,getter,在数据变动时发布消息给订阅者,触发响应的监听回调


poroxy 可以劫持整个对象,并返回一个新的对象。Proxy 不仅可以代理对象,还可以代理数组。还可以代理动态增加的属性

vue事件机制就是发布订阅

vue渲染过程
调用 compile 函数,生成 render 函数字符串 ,编译过程如下:
parse 函数解析 template,生成 ast(抽象语法树)
optimize 函数优化静态节点 (标记不需要每次都更新的内容,diff 算法会直接跳过静态节点,从而减少比较的过程,优化了 patch 的性能)
generate 函数生成 render 函数字符串
调用 new Watcher 函数,监听数据的变化,当数据发生变化时，Render 函数执行生成 vnode 对象
调用 patch 方法,对比新旧 vnode 对象,通过 DOM diff 算法,添加、修改、删除真正的 DOM 元素

Symbol 用做对象的唯一key值 包装对象
const val = Symbol();
const sym = Symbol('kval');
// Symbol(kval)
// 是不会出现在 for...in 、 for...of 的循环中，也不会被 Object.keys() 、 Object.getOwnPropertyNames() 返回。如果要读取到一个对象的 Symbol 属性，可以通过 Object.getOwnPropertySymbols() 和 Reflect.ownKeys() 取到
new Map() 他也是可迭代的 map.set map.get map.clear
优化对象结构
    对象的key直接存储为key=>value   key必须且symbol/string
    map的key可以是任意值可以是对象，null, undefined   map.set({}, 'hahha');
    如果map当做构造函数来的使用的话 参数必须是二维数组 new Map([['aaa', 'bbb']])
    所以二维数组可以转换为map
    Array.from(map)也可以转化为二维数组
new Set() 允许存储任何类型的唯一值 set.add set.has set.delete set.clear
    使用构造函数创建必须为数组
    new Set()