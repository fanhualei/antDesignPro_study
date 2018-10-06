#  Dva 使用

> 目录

* [官方网站](https://dvajs.com/)
* [快速上手](#快速上手)



​		

## 快速上手



### 新建UI页面文件

> 在pages\List\DvaExample.js 文件



```js
import React, { PureComponent } from 'react';
import { connect } from 'dva';

class DvaExample extends PureComponent {

  render() {
    return 123;
  }
}
export default DvaExample;
```





### 添加菜单

> 在router.config.js中配置菜单

```js
      // list
      {
        path: '/list',
        icon: 'table',
        name: 'list',
        routes: [
          {
            path: '/list/dvaexample',
            name: 'dvaexample',
            component: './List/DvaExample',
          },
```



####  配置菜单名称

> 在多语言文件zh-CN.js配置这个key

```js
'menu.list.dvaexample': 'dva例子',
```

>  
>
>  配置好后，就会多一个菜单:dva例子

![alt](imgs\dva-menu.png)



### 建立Model类

> 使用现成的类pages\List\models\rule.sj

```js
import { queryRule, removeRule, addRule, updateRule } from '@/services/api';

export default {
  namespace: 'rule',

  state: {
    data: {
      list: [],
      pagination: {},
    },
  },

  effects: {
    *fetch({ payload }, { call, put }) {
      const response = yield call(queryRule, payload);
      yield put({
        type: 'save',
        payload: response,
      });
    },
    *add({ payload, callback }, { call, put }) {
      const response = yield call(addRule, payload);
      yield put({
        type: 'save',
        payload: response,
      });
      if (callback) callback();
    },
    *remove({ payload, callback }, { call, put }) {
      const response = yield call(removeRule, payload);
      yield put({
        type: 'save',
        payload: response,
      });
      if (callback) callback();
    },
    *update({ payload, callback }, { call, put }) {
      const response = yield call(updateRule, payload);
      yield put({
        type: 'save',
        payload: response,
      });
      if (callback) callback();
    },
  },

  reducers: {
    save(state, action) {
      return {
        ...state,
        data: action.payload,
      };
    },
  },
};
```



### 将Model与UI connect 起来

到这里，我们已经单独完成了 model 和 component，那么他们如何串联起来呢?

dva 提供了 connect 方法。如果你熟悉 redux，这个 connect 就是 react-redux 的 connect 。

编辑 `List/DvaExample.js`，替换为以下内容：

```js
import React, { PureComponent } from 'react';
import { connect } from 'dva';
import { Table} from 'antd';

const mycolumns = [
  {
    title: '规则名称',
    dataIndex: 'name',
    key:'name',
  },
  {
    title: '创建人',
    dataIndex: 'owner',
    key:'owner',
  },
  {
    title: '描述',
    dataIndex: 'desc',
    key:'desc',
  }
];

/* dva 链接数据 */
@connect(({ rule, loading }) => ({
  rule,
  loading: loading.models.rule,
}))

class DvaExample extends PureComponent {

  // 加载数据调用
  componentDidMount() {
    const { dispatch } = this.props;
    dispatch({
      type: 'rule/fetch',
    });
  }

  //返回UI对象
  render() {
    const {
      rule: { data },
      loading,
    } = this.props;

    console.log(this.props)
    console.log(data.list)

    return (
        <Table
          style={{ marginBottom: 24 }}
          pagination={false}
          loading={loading}
          dataSource={data.list}
          columns={mycolumns}
        />
    );
  }

}

export default DvaExample;

```

执行程序，就可以看到现在的结果

![alt](imgs\dva-showlist.png)



### Model调用了Services的API

> Model类中引用了service

```js
import { queryRule, removeRule, addRule, updateRule } from '@/services/api';
//下面的函数中，就调用了，queryRule函数
    *fetch({ payload }, { call, put }) {
      const response = yield call(queryRule, payload);
      yield put({
        type: 'save',
        payload: response,
      });
    },
```



> service的代码在src/services/api.js文件中

```js
import { stringify } from 'qs';
import request from '@/utils/request';

export async function queryRule(params) {
  return request(`/api/rule?${stringify(params)}`);
}
```

这里就有一个问题，那么request工具类，到底引用的是那个文件？　



## 体验Mock 和联调

[官方文档的介绍](https://pro.ant.design/docs/mock-api-cn)

### 新建Mock

在mock文件夹下建立dvaExample.js文件

```js
export default {
  // 支持值为 Object 和 Array
  'GET /api/dva': { users: [1, 2] },

  // GET POST 可省略
  '/api/dva/1': { id: 1 },

  // 支持自定义函数，API 参考 express@4
  'POST /api/dva/create': (req, res) => { res.end('OK'); },
};
```



### 新建Service

在src/services/目录下，建立dvaExample.js

```js
import request from '@/utils/request';

export async function dva() {
  return request('/api/dva');
}

export async function dvaGet() {
  return request('/api/dva/1');
}

export async function dvaCreate() {
  return request(`/api/dva/create`);
}

```



### 新建Models

在src\pages\List\models目录下新建dva.js

```js
import { dva,dvaGet,dvaCreate} from '@/services/dvaExample';

export default {
  namespace: 'dva',

  state: {
    data: {
      list: [],
      pagination: {},
    },
  },

  effects: {
    *dva({ payload }, { call, put }) {
      const response = yield call(dva, payload);
      yield put({
        type: 'save',
        payload: response,
      });
    },
    *dvaGet({ payload, callback }, { call, put }) {
      const response = yield call(dvaGet, payload);
      yield put({
        type: 'save',
        payload: response,
      });
      if (callback) callback();
    },
    *dvaCreate({ payload, callback }, { call, put }) {
      const response = yield call(dvaCreate, payload);
      yield put({
        type: 'save',
        payload: response,
      });
      if (callback) callback();
    },
  },

  reducers: {
    save(state, action) {
      return {
        ...state,
        data: action.payload,
      };
    },
  },
};

```



### 新建UI Page显示数据

在src\pages\List\目录下新建Dva.js ，并关联数据

```js
import React, { PureComponent } from 'react';
import { connect } from 'dva';
import { Table} from 'antd';



/* dva 链接数据 */
@connect(({ dva, loading }) => ({
  dva,
  loading: loading.models.dva,
}))

class Dva extends PureComponent {

  // 加载数据调用
  componentDidMount() {
    const { dispatch } = this.props;
    dispatch({
      type: 'dva/dva',
    });
  }

  render() {

    console.log(this.props)

    console.log(this.props.dva.data.users[0])

    return (
      <div>dddddd显示第一个数值：{this.props.dva.data.users[0]}</div>
    );
  }

}

export default Dva;

```









> 参考文档

* [dva+react+ant.design](https://blog.csdn.net/qq_28008615/article/details/78110036?locationNum=4&fps=1)

