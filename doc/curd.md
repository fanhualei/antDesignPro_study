# 完成用户管理的 CURD 应用

[TOC]



## 程序基本架构



* Service程序，调用API接口

有两种写法，一中可以调用测试接口，另外也可以调用远程服务器接口

```js
export async function getNotice() {
  return request('/api/fanhl/getNotice');
}

export async function getCurdUsers() {
  return request('http://jsonplaceholder.typicode.com/users');
}
```

* Model类，保存state与方法
* UI 组件类，显示相关数据




## 第一步：将Model与UI关联



> model类

```js
export default {
  namespace: 'list',

  state: {
    list: [],
  },
}
```



> UI类

这里除了使用了自己撰写的fanhlList，还使用了user与project

```js
import React, { PureComponent} from 'react';
import { connect } from 'dva';


@connect(({ fanhlList,user, project, }) => ({
  fanhlList,
  user,
  project,
}))
class ListPage extends PureComponent {
    render() {
        console.log(this.props)
        return (
      		<div>
        		sssss
      		</div>
    	);
    }     
}    

export default ListPage;

```



> 在界面中可以看到应用的三个Model

![alt](imgs\cura-1.png)



## 第二步：添加检索函数与表格显示



### 在model类中追加检索函数

fetch用来检索数据，reducers用来修改state

```js
  effects: {
    *fetch({ payload }, { call, put }) {
      const response = yield call(queryFakeList, payload);
      yield put({
        type: 'queryList',
        payload: Array.isArray(response) ? response : [],
      });
    },
  },
  reducers: {
    queryList(state, action) {
      return {
        ...state,
        list: action.payload,
      };
    },
  },
```

#### API接口的模拟

* service函数

```js
export async function queryFakeList(params) {
  return request(`/api/fake_list?${stringify(params)}`);
}
```

* mock模拟的函数

mock模拟了服务器的操作函数

```js
function getFakeList(req, res) {
  const params = req.query;

  const count = params.count * 1 || 20;

  const result = fakeList(count);
  sourceData = result;
  return res.json(result);
}


```



```jsx
function fakeList(count) {
  const list = [];
  for (let i = 0; i < count; i += 1) {
    list.push({
      id: `fake-list-${i}`,
      owner: user[i % 10],
      title: titles[i % 8],
      avatar: avatars[i % 8],
      cover: parseInt(i / 4, 10) % 2 === 0 ? covers[i % 4] : covers[3 - (i % 4)],
      status: ['active', 'exception', 'normal'][i % 3],
      percent: Math.ceil(Math.random() * 50) + 50,
      logo: avatars[i % 8],
      href: 'https://ant.design',
      updatedAt: new Date(new Date().getTime() - 1000 * 60 * 60 * 2 * i),
      createdAt: new Date(new Date().getTime() - 1000 * 60 * 60 * 2 * i),
      subDescription: desc[i % 5],
      description:
        '在中台产品的研发过程中，会出现不同的设计规范和实现方式，但其中往往存在很多类似的页面和组件，这些类似的组件会被抽离成一套标准规范。',
      activeUser: Math.ceil(Math.random() * 100000) + 100000,
      newUser: Math.ceil(Math.random() * 1000) + 1000,
      star: Math.ceil(Math.random() * 100) + 100,
      like: Math.ceil(Math.random() * 100) + 100,
      message: Math.ceil(Math.random() * 10) + 10,
      content:
        '段落示意：蚂蚁金服设计平台 ant.design，用最小的工作量，无缝接入蚂蚁金服生态，提供跨越设计与开发的体验解决方案。蚂蚁金服设计平台 ant.design，用最小的工作量，无缝接入蚂蚁金服生态，提供跨越设计与开发的体验解决方案。',
      members: [
        {
          avatar: 'https://gw.alipayobjects.com/zos/rmsportal/ZiESqWwCXBRQoaPONSJe.png',
          name: '曲丽丽',
          id: 'member1',
        },
        {
          avatar: 'https://gw.alipayobjects.com/zos/rmsportal/tBOxZPlITHqwlGjsJWaF.png',
          name: '王昭君',
          id: 'member2',
        },
        {
          avatar: 'https://gw.alipayobjects.com/zos/rmsportal/sBxjgqiuHMGRkIjqlQCd.png',
          name: '董娜娜',
          id: 'member3',
        },
      ],
    });
  }

  return list;
}
```







### 在UI的初始化函数中调用异步函数



```js
class ListPage extends PureComponent {
  componentDidMount() {
    const { dispatch } = this.props;
    dispatch({
      type: 'fanhlList/fetch',
      payload: {
        count: 5,
      },
    });
  }

  render() {
    console.log(this.props)
    return (
      <div>
        sssss
      </div>
    );
  }
}
```



### 使用antd的表格控件显示数据

在代码中`const {fanhlList}=this.props;`，得到fanhlList，并把这个属性绑定到list控件上。



```jsx
  render() {

    const {fanhlList}=this.props;
    console.log(this.props)
    return (
      <div>
        <List
          size="large"
          rowKey="id"
          dataSource={fanhlList.list}
          renderItem={item => (
            <List.Item
              actions={[
                <a>
                  编辑
                </a>,
              ]}
            >
              <List.Item.Meta
                avatar={<Avatar src={item.logo} shape="square" size="large" />}
                title={<a href={item.href}>{item.title}</a>}
                description={item.subDescription}
              />
              dddd
            </List.Item>
          )}

        />
      </div>
    );
  }
```



## 第三步：添加编辑、删除菜单



### 添加编辑菜单

> 定义菜单组件

```js
const MoreBtn = props => (
  <Dropdown
    overlay={
      <Menu>
        <Menu.Item key="edit">编辑</Menu.Item>
        <Menu.Item key="delete">删除</Menu.Item>
      </Menu>
    }
  >
    <a>
      更多 <Icon type="down" />
    </a>
  </Dropdown>
);
```



> 在list的actions中引用MoreBtn组件

```jsx
      <List
        size="large"
        rowKey="id"
        dataSource={fanhlList.list}
        renderItem={item => (
          <List.Item
            actions={[
              <a>
                编辑
              </a>,
              <MoreBtn current={item} />,
            ]}
          >
            <List.Item.Meta
              avatar={<Avatar src={item.logo} shape="square" size="large" />}
              title={<a href={item.href}>{item.title}</a>}
              description={item.subDescription}
            />
            <ListContent data={item} />
          </List.Item>
          )}
      />
```



### 添加编辑对应函数

#### 为编辑按钮添加一个edit函数

比如一个button放在一个form中，这个button的Default就是提交（submit），但如果你不想让他提交，就可以用`e.preventDefault();`

```jsx
<List.Item
  actions={[
    <a onClick={e=>{
      e.preventDefault();
      this.showEditModal(item);
      }}
    >
      编辑
    </a>,
    <MoreBtn current={item} />
  ]}
>
```

在函数体内做一个函数

```jsx
  showEditModal=(item)=>{
    console.log("==========showEditModal==============")
    console.log(item)
  }
```



### 添加下拉菜单删除

```jsx
<Dropdown
  overlay={
    <Menu onClick={e=>{this.editAndDelete(e.key,props.current);}}>
      <Menu.Item key="edit">编辑</Menu.Item>
      <Menu.Item key="delete">删除</Menu.Item>
    </Menu>
  }
>
  <a>
    更多 <Icon type="down" />
  </a>
</Dropdown>
```

为点击项目添加一个函数

```jsx
editAndDelete=(key,item)=>{
  console.log("==========editAndDelete==============")
  console.log(item)
  console.log(key)
}
```



## 第四步：实现删除功能

删除功能需要删除服务器上的数据，所以需要在service中有相关的功能，同时需要修改model

### 在model中追加功能

在commit中隐藏了三个功能，add update dele

`Object.keys(payload)`返回一个由一个给定对象的自身可枚举属性组成的数组，例如得到payload的属性。

````jsx
*subumit({payload},{call,put}) {
  console.log(payload);
  let callback;
  if(payload.id){
    callback=Object.keys(payload).length===1?removeFakeList:updateFakeList;
  }else{
    callback=addFakeList;
  }
  const response=yield call(callback,payload);
  yield put({
    type:'queryList',
    payload:response,
  });
},
````

### 在UI中添加相关的deleteItem函数

```jsx
  const deleteItem=(id)=>{
    dispatch({
      type: 'fanhlList/submit',
      payload: { id },
    });
  }
```



## 第五步：实现编辑功能



### 添加一个对话框

```jsx
const modalFooter = done
  ? { footer: null, onCancel: this.handleDone }
  : { okText: '保存', onOk: this.handleSubmit, onCancel: this.handleCancel };


<Modal
  title={done ? null : `任务${current.id ? '编辑' : '添加'}`}
  className={styles.standardListForm}
  width={640}
  bodyStyle={done ? { padding: '72px 0' } : { padding: '28px 0 0' }}
  destroyOnClose
  visible={visible}
  {...modalFooter}
>
  {getModalContent()}
</Modal>
```



> Form的内容

```jsx
const getModalContent = () => {
  if (done) {
    return (
      <Result
        type="success"
        title="操作成功"
        description="一系列的信息描述，很短同样也可以带标点。"
        actions={
          <Button type="primary" onClick={this.handleDone}>
            知道了
          </Button>
        }
        className={styles.formResult}
      />
    );
  }
  return (
    <Form onSubmit={this.handleSubmit}>
      <FormItem label="任务名称" {...this.formLayout}>
        {getFieldDecorator('title', {
          rules: [{ required: true, message: '请输入任务名称' }],
          initialValue: current.title,
        })(<Input placeholder="请输入" />)}
      </FormItem>
    </Form>
  );
};
```



### 添加对话框对应的函数

`form.validateFields`用来判断表单是否输入正确，然后调用服务器函数进行提交。

这里有一个疑问，如果服务器端出现错误怎么办呢？

```jsx
handleDone = () => {
  this.setState({
    done: false,
    visible: false,
  });
};
handleCancel = () => {
  this.setState({
    visible: false,
  });
};
handleSubmit = e => {
  e.preventDefault();
  const { dispatch, form } = this.props;
  const { current } = this.state;
  const id = current ? current.id : '';

  // 前端判断输入项是否正确
  form.validateFields((err,fieldsValue)=>{
    if (err) return;
    this.setState({
      done: true,
    });
    console.log(fieldsValue)
    dispatch({
      type: 'fanhlList/submit',
      payload: { id, ...fieldsValue },
    });
  });
};
```



## 第六步:实现添加功能

添加一个追加按钮

```jsx
<Button
  type="dashed"
  style={{ width: '100%', marginBottom: 8 }}
  icon="plus"
  onClick={this.showModal}
>
  添加
</Button>
```

添加相关的函数

```jsx

showModal = () => {
  this.setState({
    visible: true,
    current: undefined,
  });
};
```













