# 登陆逻辑



[登陆组件](http://pro.ant.design/components/login-cn)



## Service模块

使用了多个模块

```jsx
import { fakeAccountLogin, getFakeCaptcha } from '@/services/api';
import { setAuthority } from '@/utils/authority';
import { getPageQuery } from '@/utils/utils';
import { reloadAuthorized } from '@/utils/Authorized';
```



* `fakeAccountLogin`  : 登陆函数，返回当前登陆状态
* `getFakeCaptcha` :得到验证码
* `setAuthority` :设置当前角色
* `getPageQuery`：得到一个URL中`?`后面的内容
* `reloadAuthorized`：刷新框架的角色信息



>setAuthority：将角色添加到localStorage

```jsx
export function setAuthority(authority) {
  const proAuthority = typeof authority === 'string' ? [authority] : authority;
  return localStorage.setItem('antd-pro-authority', JSON.stringify(proAuthority));
}
```





## Model模块



文件：src/models/login.js



> state

```jsx
state: {
  status: undefined,
},
```



> effects



* **login登陆**
  * 从API得到登陆结果。并将结果更新到state
    * 在更新state之前要`setAuthority()`，去设置当前的状态
  * 如果返回的结果成功
    * 刷新当前的`Authory`: `reloadAuthorized()`
    * 如果有redirect，那么跳转到对应的URL，否则跳转到`/`
* **getCaptcha得到手机验证码**
* **logout**
  * 将登陆的state中status设置成false。
  * 如果URL不等于`/user/login`，也不等于`redirect`
    * 挑转到login，并设置redirect



> reducers

* **changeLoginStatus**

改变登陆状态

```jsx
setAuthority(payload.currentAuthority);
return {
  ...state,
  status: payload.status,
  type: payload.type,
};
```







## UI模块



###  登陆login

在登陆模块，可以通过用户名与密码进行登陆，也可以通过手机来进行登陆。



>  目录下有一个login1.js，实现的远程登陆。如果密码输入正确，服务器返回的内容是：

```json
{
	roles: [{roleId: 1, rolename: "ROLE_ADMIN", description: "管理员", sort: 0}], 
	token: "Bearer eyJhbGciOiJIUzUxMiJ9.eyJjcmVhdGVkIjoxNTU2MT…WoKdQaPYbUC5AdcCBgmsNZgIMGaKpOOJ4qmfKbb37Yho31DTg"
}
```



### 登出 logout







### 自动登陆 AutoLogin















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