# 登陆逻辑



[登陆组件](http://pro.ant.design/components/login-cn)



##  登陆login



如果密码输入正确，服务器返回的内容是：

```json
{
	roles: [{roleId: 1, rolename: "ROLE_ADMIN", description: "管理员", sort: 0}], 
	token: "Bearer eyJhbGciOiJIUzUxMiJ9.eyJjcmVhdGVkIjoxNTU2MT…WoKdQaPYbUC5AdcCBgmsNZgIMGaKpOOJ4qmfKbb37Yho31DTg"
}
```







## 登出 logout





## 自动登陆 AutoLogin











































## 注释



Promise  then catch

```jsx
  onGetCaptcha = () =>
    new Promise((resolve, reject) => {
      this.loginForm.validateFields(['mobile'], {}, (err, values) => {
        if (err) {
          reject(err);
        } else {
          const { dispatch } = this.props;
          dispatch({
            type: 'login/getCaptcha',
            payload: values.mobile,
          })
            .then(resolve)
            .catch(reject);
          message.warning(formatMessage({ id: 'app.login.verification-code-warning' }));
        }
      });
    });
```





d.ts 文件



static defaultProps  作用