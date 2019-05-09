# 跨域处理

以前我的跨域处理，是在服务器端完成了，但是看了ant design pro官方的教程，发现可以在客户端进行。

> 主要参考文档

* [官方实战教程-在 model 中请求服务端数据](https://www.yuque.com/ant-design/course/ig6mzb)
* [UmiJs跨域详细说明](https://umijs.org/zh/config/#proxy)



## 在`config.js`配置跨域代理

输入：/serverApi/users

输出：https://jsonplaceholder.typicode.com/users



```jsx
proxy: {
	'/serverApi': {
	  target: 'https://jsonplaceholder.typicode.com',
	  changeOrigin: true,
	  pathRewrite: {"^/serverApi" : ""}
	},
},

```



## 在Model中调用这个查询

输入`/serverApi/users`，通过代理，调用跨域处理

```jsx
effects: {
  *queryInitCards(_, sagaEffects) {
    const { call, put } = sagaEffects;
    const endPointURI = '/serverApi/users';
    const puzzle = yield call(request, endPointURI);  
  yield put({ type: 'query', payload: puzzle });

  }
},

```



## 疑问：header中的内容是否可以传递到服务器

