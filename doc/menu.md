# 菜单



[官方说明](http://pro.ant.design/docs/router-and-nav-cn)



* 带参数的路由



## 基本结构

在这一部分，脚手架通过结合一些配置文件、基本算法及工具函数，搭建好了路由和菜单的基本框架，主要涉及以下几个模块/功能：

- `路由管理` 通过约定的语法根据在 [`router.config.js`](https://github.com/ant-design/ant-design-pro/blob/master/config/router.config.js) 中配置路由。
- `菜单生成` 根据路由配置来生成菜单。菜单项名称，嵌套路径与路由高度耦合。
- `面包屑` 组件 [PageHeader](http://pro.ant.design/components/PageHeader) 中内置的面包屑也可由脚手架提供的配置信息自动生成。

下面简单介绍下各个模块的基本思路，如果你对实现过程不感兴趣，只想了解应该怎么实现相关需求，可以直接查看[需求实例](http://pro.ant.design/docs/router-and-nav#需求实例)。

### 路由

目前脚手架中所有的路由都通过 [`router.config.js`](https://github.com/ant-design/ant-design-pro/blob/master/config/router.config.js) 来统一管理，在 umi 的配置中我们增加了一些参数，如 `name`，`icon`，`hideChildrenInMenu`，`authority`，来辅助生成菜单。其中：

- `name` 和 `icon`分别代表生成菜单项的文本和图标。

- `hideChildrenInMenu` 用于隐藏不需要在菜单中展示的子路由。用法可以查看 `分步表单` 的配置。

- `hideInMenu` 可以在菜单中不展示这个路由，包括子路由。效果可以查看 `exception/trigger`页面。

- `authority` 用来配置这个路由的权限，如果配置了将会验证当前用户的权限，并决定是否展示。

  > 你可能注意到配置中的 `name` 和菜单实际展示的不同，这是因为我们使用了全球化组件的原因，具体参见 [i18n](http://pro.ant.design/docs/i18n)

### 菜单

菜单根据 [`router.config.js`](https://github.com/ant-design/ant-design-pro/blob/master/config/router.config.js) 生成，具体逻辑在 `src/models/menu.js` 中的 `formatter` 方法实现。

> 如果你的项目并不需要菜单，你可以直接在 `BasicLayout` 中删除 `SiderMenu` 组件的挂载。并在 [`src/layouts/BasicLayout`](https://github.com/ant-design/ant-design-pro/blob/master/src/layouts/BasicLayout.js#L227) 中设置 `const MenuData = []`。





## 代码分析



### menu.js



####  总览



#### 1：得到当前目录的routes

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



#### 2：通过`formatter`函数得到改造后的菜单



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



#### 3：过滤原始菜单`filterMenuData`得到`menuData`



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







#### 4：得到面包屑 `memoizeOneGetBreadcrumbNameMap`



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











#### 