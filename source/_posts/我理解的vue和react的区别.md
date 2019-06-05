---
title: 我理解的vue和react的区别
date: 2019-03-11 17:57:04
tags:
---
vue 和 react 都是MVVM模式，组件化，整体功能类似，但是使用时间长了，设计思路是很多不同的。
# 数据是不可变的
   react的思想是函数式思维，把组件设计为业务组件和木偶组件，木偶组件是纯组件，状态和逻辑通过参数传入。所以在react中，是单向数据流，并且推崇结合immutable.js来实现数据不可变。
   react在setState后会重新走渲染的流程，如果shouldComponentUpdate返回的是true，就继续渲染，如果返回false。就不会重新渲染。所以组件要拆分，拆的越细越好。
   vue的思想是响应式的，也就是基于数据可变的。通过watcher来实现监听，当属性变化的时候，响应式的更新对应的dom节点。
   那么，react的性能优化需要手动去做，比如拆分组件，初始化状态的位置，数据请求，数据仓库的使用，都需要手动去思考怎么存放。vue的性能优化是自动的，但是vue的响应式的机制也有问题，当state特别多的时候，watcher也会很多，会导致卡顿。
# 生成html的方式
   react的思路是用js生成html，所以有jsx。vue是把html，css和js组合在了一起，用各自处理的方式，比如html是用模板引擎来处理，css 和 js都是结合webpack的loader来处理。
# 写法不同
   react是类式的写法，vue是声明式的写法。
# 扩展方式
   react可以通过高阶组件来扩展，vue是使用mixins来扩展。
# 周边生态
   react做的事情很少，都交给社区去做。vue很多东西都是内置的。比如redux的combineReducer对应着vue的modules，redux的reducer是返回一个全新的state，vuex的mutation是直接改变原始数据。