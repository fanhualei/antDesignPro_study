# 通过服务器控制菜单

我实验了一下，把这个功能移到BasicLayout的componentDidMount（）



## 当前的bug

* 登陆后再刷新就会出现那个没有权限的菜单
* 如果模拟服务器延时后，是否可以刷新出来的问题



## 定义一个util函数，authRoutes.js

```
function ergodicRoutes(routes, authKey, authority) {
  routes.forEach(element => {
    if (element.path === authKey) {
      if (!element.authority) element.authority = []; // eslint-disable-line
      Object.assign(element.authority, authority || []);
    } else if (element.routes) {
      ergodicRoutes(element.routes, authKey, authority);
    }
    return element;
  });
}


export function patchRoutes(authRoutes) {
  console.log("authRoutes",authRoutes)
  Object.keys(authRoutes).map(authKey =>
    ergodicRoutes(window.g_routes, authKey, authRoutes[authKey].authority)
  );
}
```



## 模拟一个mock函数与service

将基本详情页的权限设置成user，同时在api中调用

```
export default {
  '/api/auth_routes': {
    '/form/advanced-form': { authority: ['admin', 'user'] },
    '/profile/basic': { authority: ['user'] },
  },
};
```

> api service

```
export async function getAuthRoutes() {
  return request(`/api/auth_routes`);
}
```



## menu model中定义相关函数

```
import { getAuthRoutes } from '@/services/api';
import { patchRoutes } from '@/utils/authRoutes';
..................................
*authRoutes({ payload }, { call }) {
  const authRoutes = yield call(getAuthRoutes, payload);
  patchRoutes(authRoutes)
},
```



## 在BasicLayout调用这个函数

```
componentDidMount() {
  const {
    dispatch,
    route: { routes, path, au
  } = this.props;
  dispatch({
    type: 'menu/authRoutes',
  });
.............................
}
```



## 打开程序进行测试

用admin进行登陆，发现基本详情页被屏蔽了，在地址栏输入会出现权限不足的菜单