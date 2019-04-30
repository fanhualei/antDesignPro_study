## 常见问题



#### 如何捕获错误，并且在页面上展示？

例如服务器返回的是，如果在现有的ant design pro中捕获这个错误。

```jsx
{
    "status": 400,
    "error": "Bad Request",
    "message": "参数无效",
    "path": "/result/para1",
    "exception": "javax.validation.ConstraintViolationException",
    "errors": [
        {
            "fieldName": "email",
            "message": "不是一个合法的电子邮件地址"
        },
        {
            "fieldName": "name",
            "message": "长度需要在6和50之间"
        }
    ],
    "timestamp": "2018-03-11T13:34:30.611+0000"
}

```



### 列表 编辑 新增 三个页面之间的跳转逻辑是什么？

什么时候做弹出？ 什么时候打开一个新页面，两个具体的逻辑是什么？

* 弹出编辑框
  * 新增一个记录，怎么显示在当前列表中？ 如果有分页的话？ 刷新到第一页。
  * 编辑记录时，
    * 成功了，如果将变更后的记录显示在当前页面上
    * 失败了，如何提示
* 打开一个新页面



###  JWT token保存在cookie中，还是其他地方

有没有现成的例子。



### 动态菜单





### 在一个列表中做多个条件的筛选与排序，前后台怎么设计，能做的比较通用化



### 发现官方的ant design pro 版本升级了，如何把新的版本Merge到当前的工程中？





### 前端开发的过程中有什么规范吗？



### 如何进行js的Debug?



### 如何将jwt保存到cookie-httpOnly中



```
Access to fetch at 'https://wx.runzhichina.com/wechat/ps/selectByPsId?psDetailId=10000' from origin 'http://127.0.0.1:8000' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: It does not have HTTP ok status.

```



