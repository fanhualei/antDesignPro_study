# 路由-菜单-权限



##  基本套路

在开发新程序，主要步骤如下

* 在`router.config.js` 中定义路由与菜单。（不建议定义权限）
* 在`/api/auth_routes`API函数中返回每个路由对应的菜单。
* 后面会根据每个人，登陆的author来自动显示菜单与路由权限



> 举例：api/auth_routes定义

/list只能由user用户来访问

```jsx
export default {
  '/api/auth_routes': {
    '/form/advanced-form': { authority: ['admin', 'user'] },
    '/list': { authority: ['user'] },
  },
};
```



## 参考文档

[官方说明](http://pro.ant.design/docs/router-and-nav-cn)

* 带参数的路由





## 代码分析-menu.js





### 1：得到当前目录routes

例如得到了path:'/'的 routes

```jsx
{
  path: '/',
  component: '../layouts/BasicLayout',
  Routes: ['src/pages/Authorized'],
  routes: [
    // dashboard
    { path: '/', redirect: '/dashboard/analysis', authority: ['admin', 'u
    {
      path: '/dashboard',
      name: 'dashboard',
      icon: 'dashboard',
      routes: [
        {
          path: '/dashboard/analysis',
          name: 'analysis',
          component: './Dashboard/Analysis',
        },
        {
          path: '/dashboard/monitor',
          name: 'monitor',
          component: './Dashboard/Monitor',
        },
        {
          path: '/dashboard/workplace',
          name: 'workplace',
          component: './Dashboard/Workplace',
        },
      ],
    },
}    
```



### 2：通过`formatter`函数得到改造后的菜单



```jsx
const memoizeOneFormatter = memoizeOne(formatter, isEqual);
const originalMenuData = memoizeOneFormatter(routes, authority, path);
```

实际上调用了formatter函数。

> 使用的第三方函数库

- 缓存函数

  - [记忆化技术memoize-one](https://liyang0207.github.io/2018/10/11/%E3%80%8A%E8%AE%B0%E5%BF%86%E5%8C%96%E6%8A%80%E6%9C%AFmemoize-one%E3%80%8B/)

- lodash/isEqual

  - [js ===（全等号）的理解 以及 与lodash.isEqual的区别](https://blog.csdn.net/qq_29854831/article/details/80099173)

- lodash库

  - [学习lodash——这一篇就够用](https://blog.csdn.net/qq_35414779/article/details/79077618)

  

>  得到这么一个对象

```json
[
  {
    "path": "/dashboard",
    "name": "Dashboard",
    "icon": "dashboard",
    "locale": "menu.dashboard",
    "children": [
      {
        "path": "/dashboard/analysis",
        "name": "分析页",
        "exact": true,
        "locale": "menu.dashboard.analysis"
      },
      {
        "path": "/dashboard/monitor",
        "name": "监控页",
        "exact": true,
        "locale": "menu.dashboard.monitor"
      },
      {
        "path": "/dashboard/workplace",
        "name": "工作台",
        "exact": true,
        "locale": "menu.dashboard.workplace"
      }
    ]
  },
```



> 程序逻辑

* Map For循环传入的routes得到每个item
  * 如果item的name与path为空，就返回Null
  * 生成locale
    * 如果(parentName && parentName !== '/') ，那么前面要添加上parentName，主要是子菜单。
    * 否则的话，locale= 前面添加上menu。主要是顶级菜单
  * 生成name，主要是中文或多语言的name
    * 如果启动多语言，那么就`formatMessage`
  * 组装result
    * 保留原有的项目
    * 替换如下
      * name
      * locale(新追加)
      * authority(如果没有就取父节点的，如果有就拿自己的)
    * 判断当前item是否有子`routes`，如果有就将`result`的`children`复制
      * 如果有,就递归的调用，并得到结果。
* `.filter(item => item)`过滤到空的数据



### 3：过滤原始菜单`filterMenuData`得到`menuData`



> 函数逻辑

* 过滤掉那些name为空，并且，`hideInMenu`=false的数据
* 循环数据，`Authorized.check`，过滤掉那些不符合权限的数据
  * [Authorized.check使用说明]([#](http://pro.ant.design/components/authorized-cn#authorizedcheck))



```jsx
[
  {
    "path": "/dashboard",
    "name": "Dashboard",
    "icon": "dashboard",
    "locale": "menu.dashboard",
    "children": [
      {
        "path": "/dashboard/analysis",
        "name": "分析页",
        "exact": true,
        "locale": "menu.dashboard.analysis"
      },
      {
        "path": "/dashboard/workplace",
        "name": "工作台",
        "exact": true,
        "locale": "menu.dashboard.workplace"
      }
    ]
  },
]
```







### 4：得到面包屑 `memoizeOneGetBreadcrumbNameMap`



```jsx
{
  "/dashboard/analysis": {
    "path": "/dashboard/analysis",
    "name": "分析页",
    "exact": true,
    "locale": "menu.dashboard.analysis"
  },
  "/dashboard/monitor": {
    "path": "/dashboard/monitor",
    "name": "监控页",
    "authority": [
      "user"
    ],
    "exact": true,
    "locale": "menu.dashboard.monitor"
  },
  "/dashboard/workplace": {
    "path": "/dashboard/workplace",
    "name": "工作台",
    "exact": true,
    "locale": "menu.dashboard.workplace"
  },
  "/dashboard": {
    "path": "/dashboard",
    "name": "Dashboard",
    "icon": "dashboard",
    "locale": "menu.dashboard",
    "children": [
      {
        "path": "/dashboard/analysis",
        "name": "分析页",
        "exact": true,
        "locale": "menu.dashboard.analysis"
      },
      {
        "path": "/dashboard/monitor",
        "name": "监控页",
        "authority": [
          "user"
        ],
        "exact": true,
        "locale": "menu.dashboard.monitor"
      },
      {
        "path": "/dashboard/workplace",
        "name": "工作台",
        "exact": true,
        "locale": "menu.dashboard.workplace"
      }
    ]
  },
}  
```



## 代码分析-app.js

[app.js的官方说明](https://umijs.org/zh/guide/runtime-config.html#%E4%B8%BA%E4%BB%80%E4%B9%88%E6%9C%89%E8%BF%90%E8%A1%8C%E6%97%B6%E9%85%8D%E7%BD%AE%EF%BC%9F)



> 获得指定的权限

```jsx
export function render(oldRender) {
  fetch('/api/auth_routes')
    .then(res => res.json())
    .then(
      ret => {
        authRoutes = ret;
        oldRender();
      },
      () => {
        oldRender();
      }
    );
}
```



> 修改路由

```jsx
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

export function patchRoutes(routes) {
  Object.keys(authRoutes).map(authKey =>
    ergodicRoutes(routes, authKey, authRoutes[authKey].authority)
  );
  window.g_routes = routes;
}
```





#### 老的版本

老的版本会根据服务器得到菜单值

````jsx
import fetch from 'dva/fetch';

export const dva = {
  config: {
    onError(err) {
      err.preventDefault();
    },
  },
};

let authRoutes = {};

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

export function patchRoutes(routes) {
  Object.keys(authRoutes).map(authKey =>
    ergodicRoutes(routes, authKey, authRoutes[authKey].authority)
  );
  window.g_routes = routes;
}

export function render(oldRender) {
  fetch('/api/auth_routes')
    .then(res => res.json())
    .then(
      ret => {
        authRoutes = ret;
        oldRender();
      },
      () => {
        oldRender();
      }
    );
}
````






