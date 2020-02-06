# Vue 面试题

标签（空格分隔）： vue

---

### 第一题： v-if和v-for哪个优先级高？如果两个同时出现，应该怎么优化得到更好的性能？

> 当 Vue 处理指令时，v-for 比 v-if 具有更高的优先级*（永远不要把 v-if 和 v-for 同时用在同一个元素上。）*（可写个demo输出render查看）
> 如果同时出现，每次渲染都会先执行循环，在进行判断；
> 无论如何循环都不可避免，浪费性能

- 同时出现的两种情况处理
 1. 代码：（`v-for`遍历初始数据，`v-if`根据item数据判断）
    优化：过滤一个列表中的项目，在这种情形下，将初始数据替换为一个计算属性，让其返回过滤后的列表。 
 2. 代码：（`v-for`遍历初始数据，`v-if`根据初始数据判断）
    优化：避免渲染本应该被隐藏的列表,这种情形下，请将 v-if 移动至容器元素上 。


### 第二题： Vue组件data选项为什么必须是个函数而Vue的根实例则没有此限制

> 1、vue组件中data值不能为对象，因为对象是引用类型，组件可能会被多个实例同时引用。如果data值为对象，将导致多个实例共享一个对象，其中一个组件改变data属性值，其他实例也会受到影响

    var MyComponent = function() {}
    MyComponent.prototype.data = {
      a: 1,
      b: 2,
    }
    
    var component1 = new MyComponent()
    var component2 = new MyComponent()
    
    component1.data.a === component2.data.a // true
    component1.data.b = 5
    component2.data.b // 5
> 
>  2、vue组件data为函数的原因，data为函数，通过return返回对象的拷贝，致使每个实例都有自己独立的对象，实例之间可以互不影响的改变data属性值

所以确保每一个实例的`data`属性都是独立的，不会互相影响。`vue`组件里面的`data`必须是一个函数，相比`vue`的`data`，`react`的`setData`更形象

而在Vue实例创建中，只有一个，不用担心会被复用。


### 第三题： vue中key的作用和工作原理吗？说说你对它的理解

key的作用是为了在diff算法执行时更快的找到对应的节点，提高diff速度
> 当使用 v-for 正在更新已渲染过的元素列表时，key的作用让每个item有一个唯一的识别身份，可以下标值index或者id, 主要是为了vue精准的追踪到每一个元素，高效的更新虚拟DOM。


### 第四题： 怎么理解vue中的diff算法

整体策略：深度优先、同层比较

 1. diff宣发是虚拟DOM技术的必然产物，通过新旧虚拟DOM对比，将变化的更新在真实DOM；（同层比较）
 2. vue 2.x中为了降低Watcher粒度，每个组件只有一个Watcher与之对应，只有引入diff才能精确找到发生变化的地方
 3. vue中的diff执行的时刻是组件实例执行其更新函数时，它会对比上次渲染结果和新的渲染结果，此过程称为patch
 4. diff整体遵循深度优先、同层比较的策略。比较两组子节点是算法的重点。首先假设头尾节点可能相同做4次比对尝试，如果没有找到相同节点才会按照通用方式遍历查找。查找结束在按情况处理剩下的节点；借助key通常可以精准的找到相同的节点，因为整个patch过程非常高效

### 第五题： 谈一谈对vue组件化的理解
### 第六题： 谈一谈对vue的设计原则的理解
### 第七题： vue为什么要求足见模板只能有一个根元素
### 第八题： 谈谈你对mvvm、mvp、mvc的理解
### 第九题： 谈谈你对vue组件之间通信的理解
### 第十题： 你了解哪些vue性能优化方法
### 第十一题： 你知道vue3有哪些新特性吗？以及会带来的影响
### 第十二题： vue如果想扩展某个现有的组件时应该怎么做？
### 第十三题： watch和computed的区别以及怎么选用
### 第十四题： 谈谈你对vue生命周期的理解

> vue 所有的生命周期都是通过内部全局 `callHook` 函数执行

    
 1. 初始化
    - `beforeCreate`  访问不到 `data` `methods` 数据，数据还没初始化
    - injections（注射器） reactivity（响应）给数据添加观察者
    - `created` 因为已经添加观察者，所以可以访问 `data` 数据
 2. 挂载
    > 当 `data` 被修改时， 触发 `beforeUpdate` `updated`

    - `beforeMount`
    - `mounted`  挂载完成
    - `beforeUpdate`
    - `updated`
 3. 销毁
    - `beforeDestroy` 解除绑定，销毁子组件以及事件监听器
    - `destroyed` 销毁完毕

### 第十五题： 谈谈你对vuex使用及其理解？

 1. vuex 是什么

 2. vuex 核心概念
    - state
    - mutation
    - action
    - getter
    - model

 3. 怎么做数据存储

 4. 是么情况下使用

 5. vuex 理解 => 源码


### 第十六题： nextTick的原理

> 在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

首先Vue 实现响应式并不是数据发生变化之后 DOM 立即变化，而是按一定的策略进行异步的 DOM 的更新。
异步执行的运行机制如下： 

 1. 所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。 
 2. 主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。
 3. 一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
 4. 主线程不断重复上面的第三步。

简单来说，Vue 在修改数据后，视图不会立刻更新，而是等同一事件循环中的所有数据变化完成之后，再统一进行视图更新。

    //改变数据
    vm.message = 'changed'
    
    //想要立即使用更新后的DOM。这样不行，因为设置message后DOM还没有更新
    console.log(vm.$el.textContent) // 并不会得到'changed'
    
    //这样可以，nextTick里面的代码会在DOM更新后执行
    Vue.nextTick(function(){
        console.log(vm.$el.textContent) //可以得到'changed'
    })

> 应用场景：需要在视图更新之后，基于新的视图进行操作。

总结：
 1. vue用异步队列的方式来控制DOM更新和nextTick回调先后执行
 2. microtask（微任务）因为其高优先级特性，能确保在队列中再一次时间循环前被执行完毕
 3. 因为兼容问题，vue不得不做了 microtask 向 macrotask （宏任务） 的降级方案

### 第十七题： vue的双向数据绑定的原理

> 最初的理解：Vue内部通过Object.defineProperty方法属性拦截的方式，把data对象里每个数据的读写转化成getter/setter，当数据变化时通知视图更新。

实现数据的双向绑定，首先要对数据进行劫持监听，所以我们需要设置一个监听器Observer，用来监听所有属性。如果属性发上变化了，就需要告诉订阅者Watcher看是否需要更新。因为订阅者是有很多个，所以我们需要有一个消息订阅器Dep来专门收集这些订阅者，然后在监听器Observer和订阅者Watcher之间进行统一管理的。

### 第十八题： vue-router导航钩子有哪些？

1. 全局守卫： router.beforeEach
    使用 router.beforeEach 注册一个全局前置守卫：

        const router = new VueRouter({ ... })
            router.beforeEach((to, from, next) => {
            // ...
        })
    当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫 resolve 完之前一直处于等待中。
        
    每个守卫方法接收三个参数：
        
        to: Route: 即将要进入的目标 路由对象
        from: Route: 当前导航正要离开的路由
        next: Function: 一定要调用该方法来resolve这个钩子。执行效果依赖 next 方法的调用参数。
        next(): 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是confirmed (确认的)。
    next(false): 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 from 路由对应的地址。
    next('/') 或者 next({ path: '/' }): 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向 next 传递任意位置对象，且允许设置诸如 replace: true、name: 'home' 之类的选项以及任何用在router-link的 to prop或 router.push中的选项。
    next(error): (2.4.0+) 如果传入 next 的参数是一个 Error 实例，则导航会被终止且该错误会被传递给 router.onError()注册过的回调。
    确保要调用 next方法，否则钩子就不会被 resolved。  
    
2. 全局解析守卫： router.beforeResolve
    > 和 router.beforeEach 类似，区别是：在导航被确认之前，同时在所有组件内守卫和异步路由组件被解析之后，解析守卫就被调用。

3. 全局后置钩子： router.afterEach
    > 和守卫不同的是，这些钩子不会接受 next 函数也不会改变导航本身

4. 路由独享的守卫： beforeEnter

        const router = new VueRouter({
            routes: [
                {
                    path: '/foo',
                    component: Foo,
                    beforeEnter: (to, from, next) => {
                    // ...
                }
                }
            ]
        })
    与全局前置守卫的方法参数是一样的。
    
5. 组件内的守卫： beforeRouteEnter、beforeRouteUpdate (2.2 新增)、beforeRouteLeave

            const Foo = {
                template: `...`,
                beforeRouteEnter (to, from, next) {
                    // 在渲染该组件的对应路由被 confirm 前调用
                    // 不！能！获取组件实例 `this`
                    // 因为当守卫执行前，组件实例还没被创建
                },
                //不过，你可以通过传一个回调给 next来访问组件实例。
                //在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。
                beforeRouteEnter (to, from, next) {
                    next(vm => {
                    // 通过 `vm` 访问组件实例
                    })
                },
                beforeRouteUpdate (to, from, next) {
                    // 在当前路由改变，但是该组件被复用时调用
                    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
                    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
                    // 可以访问组件实例 `this`
                },
                beforeRouteLeave (to, from, next) {
                    // 导航离开该组件的对应路由时调用
                    // 可以访问组件实例 `this`
                }
            }
       注意：beforeRouteEnter 是支持给next 传递回调的唯一守卫。对于beforeRouteUpdate 和 beforeRouteLeave 来说，this 已经可用了，所以不支持传递回调，因为没有必要了:
            
            beforeRouteUpdate (to, from, next) {
                // just use `this`
                this.name = to.params.name
                next()
            }
    离开守卫beforeRouteLeave：通常用来禁止用户在还未保存修改前突然离开。该导航可以通过 next(false) 来取消：
            
            beforeRouteLeave (to, from , next) {
                const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
                if (answer) {
                    next()
                } else {
                    next(false)
                }
            }

 
 





