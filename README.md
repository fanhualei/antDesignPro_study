# Ant Design Pro 学习笔记



> 目录



```
常用网址
https://github.com/ant-design/ant-design-pro/tree/master/src/pages

https://pro.ant.design/components/AvatarList-cn

https://ant.design/docs/react/introduce-cn


```





* 环境搭建（搭建可以开发的环境）
  * [下载代码](doc/start.md)
  * [webStorm使用](doc/webstorm.md)
  * [git的使用](doc/git.md)
* 基础知识（学习基础知识，并做两个小例子）
  * [Ant Design Pro官方文档笔记](doc/first.md)
  * [菜鸟网络-React笔记](doc/react.md)
  * [React官方学习笔记](doc/react-pro.md)
    * [做一个小的游戏](doc/game.md)
    * [createContext的说明](doc/createContext.md)
  * [学习readux](doc/redux.md)
  * [了解dva](doc/dva.md)
  * [制作一个添加删除的例子](doc/curd.md)
* antDesignPro
  * [布局](doc/layout.md)
  * [菜单](doc/menu.md)
  * [PageHeaderWrapper](doc/PageHeaderWrapper.md)
  * [Form相关](doc/form.md)
  * 单元测试jtest
  * 跨域的处理：
    * 可以在服务器段处理，也可以在客户端处理，我现在是在服务器端处理的，今后可以换到客户端
* [快速入门](doc/first.md)
  * 添加一个新页面
  * 添加一个modle
  * 配置多语言文件
  * 调用远程Rest API
  * 使用外部React组建
  * 跨域的问题
  * 如何切换mock与真实环境
  * 如何保存缓存
* 例子代码分析
  * [表单页](doc/example.md)
  * [列表页](doc/example-list.md)
  * [结果页](doc/example-result.md)
  * [异常页](doc/example-exception.md)
  * [个人主页](doc/example-account.md)
  * [dashboard](./doc/example-dashboard.md)
  * 登录注册
  * 顶部导航与设置
* API交互
  * 查询一个列表
  * 查询一个分页列表
  * 带参数的查询
  * 通过Json新增或更新记录
  * 删除一条记录
  * 复制查询条件的查询
* 预备知识
  * [dva](doc\dva.md)
* 高级应用
  * 自定义组建
  * umi脚手架
* 调试
  * 输出debug信息
  * 添加日志模块
  * mock模拟数据
  * 自动化测试
* 安全
  * 菜单权限控制
  * Session与Token登录
    * [参考如何保存session](https://www.jianshu.com/p/1329a324101d)
* 部署
  * Niginx 部署
  * 分布式部署
* 例子代码说明
  * [表单例子]((doc/example.md))
  * [列表例子](doc/example-list.md)
  * [详情页例子](doc/example-profile.md)
* antDesign组建说明







## 环境搭建

```
如何搭建一个初始的环境
```



## 添加一个新页面

```
按照基础表单页面，添加一个新的页面
```





## 疑问



> Row 与Col 怎么用？



```
<Form layout="vertical" hideRequiredMark>
   <Row gutter={16}>
   <Col lg={6} md={12} sm={24}>
```



