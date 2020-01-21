# Vue 面试题

标签（空格分隔）： vue

---

### 第一题： v-if和v-for哪个优先级高？如果两个同时出现，应该怎么优化得到更好的性能？

> 当 Vue 处理指令时，v-for 比 v-if 具有更高的优先级*（永远不要把 v-if 和 v-for 同时用在同一个元素上。）*
> 
> 

 1. 过滤一个列表中的项目，在这种情形下，将初始数据替换为一个计算属性，让其返回过滤后的列表。 （`v-for`遍历初始数据，`v-if`根据item数据判断）
 2. 避免渲染本应该被隐藏的列表,这种情形下，请将 v-if 移动至容器元素上 。  （`v-for`遍历初始数据，`v-if`根据初始数据判断）


### 第二题： Vue组件data选项为什么必须是个函数而Vue的根实例则没有此限制

 1、vue组件中data值不能为对象，因为对象是引用类型，组件可能会被多个实例同时引用。如果data值为对象，将导致多个实例共享一个对象，其中一个组件改变data属性值，其他实例也会受到影响

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

 2、vue组件data为函数的原因，data为函数，通过return返回对象的拷贝，致使每个实例都有自己独立的对象，实例之间可以互不影响的改变data属性值

> 所以确保每一个实例的`data`属性都是独立的，不会互相影响。`vue`组件里面的`data`必须是一个函数，相比`vue`的`data`，`react`的`setData`更形象

> 而在简单的Vue实例中，没什么区别，因为你app对象不会被复用。但是在组件中，因为可能在多处调用同一组件，所以为了不让多处的组件共享同一data对象，只能返回函数。

 
 





