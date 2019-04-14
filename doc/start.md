# 初始化代码



参考了官方网址：https://ant-design-pro.gitee.io/docs/getting-started-cn



## 安装[#](https://ant-design-pro.gitee.io/docs/getting-started-cn#%E5%AE%89%E8%A3%85)

从 GitHub 仓库中直接安装最新的脚手架代码。

```bash
$ git clone --depth=1 https://github.com/ant-design/ant-design-pro.git my-project
$ cd my-project
```

## 目录结构[#](https://ant-design-pro.gitee.io/docs/getting-started-cn#%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84)

我们已经为你生成了一个完整的开发框架，提供了涵盖中后台开发的各类功能和坑位，下面是整个项目的目录结构。

```bash
├── config                   # umi 配置，包含路由，构建等配置
├── mock                     # 本地模拟数据
├── public
│   └── favicon.png          # Favicon
├── src
│   ├── assets               # 本地静态资源
│   ├── components           # 业务通用组件
│   ├── e2e                  # 集成测试用例
│   ├── layouts              # 通用布局
│   ├── models               # 全局 dva model
│   ├── pages                # 业务页面入口和常用模板
│   ├── services             # 后台接口服务
│   ├── utils                # 工具库
│   ├── locales              # 国际化资源
│   ├── global.less          # 全局样式
│   └── global.js            # 全局 JS
├── tests                    # 测试工具
├── README.md
└── package.json
```

## 本地开发[#](https://ant-design-pro.gitee.io/docs/getting-started-cn#%E6%9C%AC%E5%9C%B0%E5%BC%80%E5%8F%91)

安装依赖。

```bash
$ npm install
```

> 如果网络状况不佳，可以使用 [cnpm](https://cnpmjs.org/) 进行加速。

```bash
$ npm start
```

![img](https://gw.alipayobjects.com/zos/rmsportal/uHAzKpIQDMGdmjIxZLOV.png)

启动完成后会自动打开浏览器访问 [http://localhost:8000](http://localhost:8000/)，你看到下面的页面就代表成功了。