# 快速开始



> 目录

- [新加页面与菜单](#新加页面与菜单)
- 新加Modle
- [配置多语言文件](#配置多语言文件)
- 调用远程Rest API
- 使用外部React组建





## 新加页面与菜单





### 新加菜单

> 需要编辑 config\router.config.js 文件

```js
//例如我模仿TableList，增加了一个 tableList_fan文件
      // list
      {
        path: '/list',
        icon: 'table',
        name: 'list',
        routes: [
          {
            path: '/list/table-list',
            name: 'searchtable',
            component: './List/TableList',
          },

          {
            path: '/list/table-list_fan',　　//URL中显示的路径
            name: 'searchtable_fan',　　　　　// 多语言中配置的菜单名称
            component: './List/TableList_fan',　　//对应的文件名与类名
          },
```





## 配置多语言文件



### 编辑语言文件

```
src\locales\目录下有相应的多语言文件
```

### 在代码中使用

```
// 具体代码可以参考　/src/pages/Forms/BasicForm.js

//在html标签  <FormattedMessage  />
<PageHeaderWrapper
        title={<FormattedMessage id="app.forms.basic.title" />}
        content={<FormattedMessage id="app.forms.basic.description" />}
>

//用函数的形式调用　formatMessage()

(
                <TextArea
                  style={{ minHeight: 32 }}
                  placeholder={formatMessage({ id: 'form.goal.placeholder' })}
                  rows={4}
                />
)

```



### 高级使用方式

> 传入参数









