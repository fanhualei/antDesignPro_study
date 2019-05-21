# 初始化代码

[TOC]



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



## 如何及时更新最新版本

当开发一段时间后，可能要更新pro的最新版本，这时候怎么办呢？

* git pull from pro的官方git服务器
  * 中间需要merge
* 下载完毕后，执行`npm install`
* 然后执行`npm run lint`，这时候可能出现错误
  * 原因一：自己添加的第三方组件，这时候需要自己添加。例如`npm install react-quill`
  * 原因二: 其他原因，自己按照提示进行修改。
* 当执行没有问题后，就run，看程序是否能跑起来。
* 如果能跑起来，就commit到git中。



### 如何将代码提交到自己的分支上去

1：从阿里的分支获取代码，使用了clone命令。

2：但是在提交到自己分支的时候，会出现错误。

```
[remote rejected] (shallow update not allowed)
```

3：这时候要执行fetch命令

```
## origin是阿里分支的别名
git  fetch --unshallow origin
```

```
git fetch 和git pull 的差别
1、git fetch 相当于是从远程获取最新到本地，不会自动merge
2. git pull：相当于是从远程获取最新版本并merge到本地
3. git  fetch --unshallow origin 获得完整的代码
```

4：执行完毕后，再上传到自己的分支上去。







