# Redux 学习



> 网址

* [完全理解 redux（从零实现一个 redux）](https://www.jianshu.com/p/9254c690d303)

* [官网-英文](https://redux.js.org/)

* [redux github地址](https://github.com/reduxjs/redux)

* [Redux中文-有点滞后](http://cn.redux.js.org/)

  

将以将完全理解redux看完就可以了，后面的之间看dva就行。



## 完全理解redux

为什么做redux，以及怎么做？



```
redux 主要为了管理state。
1、首先要做一个存储机制，引入了createStore，传入一个参数，将state保存起来。
2、接着出现问题了，如果有多个用户想获得这个数值怎么办。
	2.1：做了一个订阅的机制。当某个状态被修改后，可以发布给其他人
3、接着又出现问题了，如果状态被其他人偷偷修改了怎么办？
	3.1：做了一个plan【reducer】,只有通过plan才能修改，里面做了type来处理。
4、接着又出现问题了，系统中又那么多的state，不能放入一个文件吧。
	4.1:引入了combineReducers，将这些reducer给合并成一个。
5、以上查分不彻底，还要一个 state，一个 reducer 写一块。
6、在具体的开发过程，还有些切片形式的功能需要做，例如每个函数保存日志等功能，
7、如何优化中间件，让开发者更容易开发。
8、如何做退订
	
```



### 总结

到了最后，我想把 redux 中关键的名词列出来，你每个都知道是干啥的吗？

- createStore

  创建 store 对象，包含 getState, dispatch, subscribe, replaceReducer

- reducer

  reducer 是一个计划函数，接收旧的 state 和 action，生成新的 state

- action

  action 是一个对象，必须包含 type 字段

- dispatch

  `dispatch( action )` 触发 action，生成新的 state

- subscribe

  实现订阅功能，每次触发 dispatch 的时候，会执行订阅函数

- combineReducers

  多 reducer 合并成一个 reducer

- replaceReducer

  替换 reducer 函数

- middleware

  扩展 dispatch 函数！

你再看 redux 流程图，是不是大彻大悟了？





![img](https:////upload-images.jianshu.io/upload_images/15500820-bfe1fc85edf7381c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



## Redux 中文学习



Redux是用来管理state的

在下面的场景中，引入 Redux 是比较明智的：

- 你有着相当大量的、随时间变化的数据

- 你的 state 需要有一个单一可靠数据来源

- 你觉得把所有 state 放在最顶层组件中已经无法满足需要了

  

应用中所有的 state 都以一个对象树的形式储存在一个单一的 *store* 中。 惟一改变 state 的办法是触发 *action*，一个描述发生什么的对象。 为了描述 action 如何改变 state 树，你需要编写 *reducers*。





