# 完整的CURD例子

[TOC]







## eslint 注释

`/* eslint react/no-multi-comp:0 */`

在写代码的时候，有些会报错，这时候，可以使用eslint注释，来禁止报错。

[eslint 常用配置](http://www.mamicode.com/info-detail-2291421.html)





## 功能设计

基本UI设计

![alt](imgs/curd-1.png)



![alt](imgs/curd-2.png)



## 实现步骤

* 实现检索功能
  * 实现基本列表与分分页功能
  * 实现列表表头排序
  * 实现用户按照条件检索
* 编辑功能
  * 追加
  * 配置
  * 批量操作
* 详情



## 实现检索功能



### 服务器端功能



> mock:rule.js

```jsx
function getRule(req, res, u) {
  let url = u;
  if (!url || Object.prototype.toString.call(url) !== '[object String]') {
    url = req.url; // eslint-disable-line
  }

  const params = parse(url, true).query;

  let dataSource = tableListDataSource;
    ....................................................
    
  let pageSize = 10;
  if (params.pageSize) {
    pageSize = params.pageSize * 1;
  }

  const result = {
    list: dataSource,
    pagination: {
      total: dataSource.length,
      pageSize,
      current: parseInt(params.currentPage, 10) || 1,
    },
  };

  return res.json(result);
}
```



其中通过参数u得到相关url参数

```jsx
let url = u;
if (!url || Object.protot
  url = req.url; // eslin
}
const params = parse(url,
console.log(u)
console.log(url)
console.log(params)
```

在console中得到相应的数值

```
[Function: next]
/api/rule?
[Object: null prototype] {}
----------下面是带的参数
[Function: next]
/api/rule?currentPage=1&pageSize=10&name=a&status=&sorter=callNo_ascend
[Object: null prototype] {
  currentPage: '1',
  pageSize: '10',
  name: 'a',
  status: '',
  sorter: 'callNo_ascend' }
```



>  service

```jsx
export async function queryRule(params) {
  return request(`/api/rule?${stringify(params)}`);
}

export async function removeRule(params) {
  return request('/api/rule', {
    method: 'POST',
    data: {
      ...params,
      method: 'delete',
    },
  });
}

export async function addRule(params) {
  return request('/api/rule', {
    method: 'POST',
    data: {
      ...params,
      method: 'post',
    },
  });
}

export async function updateRule(params = {}) {
  return request(`/api/rule?${stringify(params.query)}`, {
    method: 'POST',
    data: {
      ...params.body,
      method: 'update',
    },
  });
}
```



### 显示表格

#### 使用的Table控件

```jsx

render() {
  console.log(this.props);
  const{fanhlRule:{data},}=this.props;
  return(
    <div>
      <Card bordered={false}>
        <Table dataSource={data.list} columns={this.columns} />
      </Card>
    </div>
  )
}

```

#### 定义表头

> columns属性

* render 生成复杂数据的渲染函数
  * 参数 Function(text, record, index) {}
* sorter: 排序函数，本地排序使用一个函数(参考 Array.sort 的 compareFunction)，需要服务端排序可设为 true
* needTotal:自己定义的属性
* filters：表头的筛选菜单项



> 使用过的控件

* Badge：[徽标](https://ant.design/components/badge-cn/)
* Fragment：React 中一个常见模式是为一个组件返回多个元素。Fragments 可以让你聚合一个子元素列表，并且不在DOM中增加额外节点。
* Divider：[分割线](https://ant.design/components/divider-cn/)



````jsx
columns = [
  {
    title: '规则名称',
    dataIndex: 'name',
    render: text => <a onClick={() => this.previewItem(text)}>{text}</a>,
  },
  {
    title: '描述',
    dataIndex: 'desc',
  },
  {
    title: '服务调用次数',
    dataIndex: 'callNo',
    sorter: true,
    render: val => `${val} 万`,
    // mark to display a total number
    needTotal: true,
  },
  {
    title: '状态',
    dataIndex: 'status',
    filters: [
      {
        text: status[0],
        value: 0,
      },
      {
        text: status[1],
        value: 1,
      },
      {
        text: status[2],
        value: 2,
      },
      {
        text: status[3],
        value: 3,
      },
    ],
    render(val) {
      return <Badge status={statusMap[val]} text={status[val]} />;
    },
  },
  {
    title: '上次调度时间',
    dataIndex: 'updatedAt',
    sorter: true,
    render: val => <span>{moment(val).format('YYYY-MM-DD HH:mm:ss')}</span>,
  },
  {
    title: '操作',
    render: (text, record) => (
      <Fragment>
        <a onClick={() => this.handleUpdateModalVisible(true, record)}>配置</a>
        <Divider type="vertical" />
        <a href="">订阅警报</a>
      </Fragment>
    ),
  },
];
````



### 排序与筛选功能

使用到了Table的onChange功能

* 分页、排序、筛选变化时触发
  * Function(pagination, filters, sorter, extra: { currentDataSource: [] })



```jsx
handleStandardTableChange = (pagination, filtersArg, sorter) => {
  const { dispatch } = this.props;
  const filters = Object.keys(filtersArg).reduce((obj, key) => {
    const newObj = { ...obj };
    newObj[key] = getValue(filtersArg[key]);
    return newObj;
  }, {});
  const params = {
    currentPage: pagination.current,
    pageSize: pagination.pageSize,
    ...filters,
  };
  if (sorter.field) {
    params.sorter = `${sorter.field}_${sorter.order}`;
  }
  dispatch({
    type: 'fanhlRule/fetch',
    payload: params,
  });
};
<Table dataSource={data.list} columns={this.columns} onChange={this.handleStandardTableChange} />

```

> 参数的例子：pagination, filtersArg, sorter

转换成下面的形式，传递给后台。

```
/api/rule?currentPage=1&pageSize=10&status=0%2C1%2C2&sorter=callNo_ascend
[Object: null prototype] {
  currentPage: '1',
  pageSize: '10',
  status: '0,1,2',
  sorter: 'callNo_ascend' }
```





![alt](imgs/curd-2-onchange.png)



其中filterArg自动转换成：status: "2,1,0"



### 按条件进行检索

由于检索条件是form所以要添加 @Form.create()

#### 功能设计

简要检索与详细检索两个。 主要的功能

* 查询按钮
* 重置按钮
* 展开按钮

![](imgs/curd-2-shearch1.png)



![](imgs/curd-2-shearch2.png)



#### state设计

在这个功能中，有两个中间变量，一个是展开状态，一个是用户输入的内容。

```jsx
state = {
  expandForm: false,
  formValues: {},
};
```

#### 显示页面

根据state的属性，判断到底显示那个页面，由于使用了form，在同一个名字下的控件，可以保留数据。

```jsx
renderForm() {
  const { expandForm } = this.state;
  return expandForm ? this.renderAdvancedForm() : this.renderSimpleForm();
}

renderSimpleForm() {
  const {
    form: { getFieldDecorator },
  } = this.props;
  return (
    <Form onSubmit={this.handleSearch} layout="inline">
      <Row gutter={{ md: 8, lg: 24, xl: 48 }}>
        <Col md={8} sm={24}>
          <FormItem label="规则名称">
            {getFieldDecorator('name')(<Input placeholder="请输入" />)}
          </FormItem>
        </Col>
        <Col md={8} sm={24}>
          <FormItem label="使用状态">
            {getFieldDecorator('status')(
              <Select placeholder="请选择" style={{ width: '100%' }}>
                <Option value="0">关闭</Option>
                <Option value="1">运行中</Option>
              </Select>
            )}
          </FormItem>
        </Col>
        <Col md={8} sm={24}>
          <span className={styles.submitButtons}>
            <Button type="primary" htmlType="submit">
              查询
            </Button>
            <Button style={{ marginLeft: 8 }} onClick={this.handleFor
              重置
            </Button>
            <a style={{ marginLeft: 8 }} onClick={this.toggleForm}>
              展开 <Icon type="down" />
            </a>
          </span>
        </Col>
      </Row>
    </Form>
  );
}
```



#### 开发函数

#####  查询函数handleSearch

````jsx
handleSearch = e => {
  e.preventDefault();
  const { dispatch, form } = this.props;
  form.validateFields((err, fieldsValue) => {
    if (err) return;
    console.log(fieldsValue)
    const values = {
      ...fieldsValue,
      updatedAt: fieldsValue.updatedAt && fieldsValue.updatedAt.valueOf(),
    };
    this.setState({
      formValues: values,
    });
    console.log(values)
    dispatch({
      type: 'fanhlRule/fetch',
      payload: values,
    });
  });
};
````

##### 重置函数 handleFormReset

```jsx
handleFormReset = () => {
  const { form, dispatch } = this.prop
  form.resetFields();
  this.setState({
    formValues: {},
  });
  dispatch({
    type: 'fanhlRule/fetch',
    payload: {},
  });
};
```

##### 展开函数

```jsx
toggleForm = () => {
  const { expandForm } = this.state;
  this.setState({
    expandForm: !expandForm,
  });
};
```



### 排序与检索做关联

这时候会发现，如果排序的时候，原先的检索结果不对了。

在state中保存了`formValues`，所以在table的handleStandardTableChange中，就可以得到检索数据了

```jsx
handleStandardTableChange = (pagination, filtersArg, sorter) => {
  const { dispatch } = this.props;
  const { formValues } = this.state; // 追加的代码
  const filters = Object.keys(filtersArg).reduce((obj, key) => {
    const newObj = { ...obj };
    newObj[key] = getValue(filtersArg[key]);
    return newObj;
  }, {});
  const params = {
    currentPage: pagination.current,
    pageSize: pagination.pageSize,
    ...formValues,  // 追加的代码
    ...filters,
  };
  if (sorter.field) {
    params.sorter = `${sorter.field}_${sorter.order}`;
  }
  dispatch({
    type: 'fanhlRule/fetch',
    payload: params,
  });
};
```



### 添加选择功能



![](imgs/curd-2-check.png)



#### 功能描述

* 有记忆功能，分页还可以记录。
* 某些记录的row，可以不能选择。
* 有汇总信息



#### 初步代码

> 在Table中添加`rowSelection={rowSelection}`

```jsx
<Table
  dataSource={data.list}
  rowSelection={rowSelection}
  columns={this.columns}
  onChange={this.handleStandardTableChange}
/>
```

> 添加`rowSelection`函数

```jsx
const rowSelection = {
  onChange: (selectedRowKeys, selectedRows) => {
    console.log(`selectedRowKeys: ${selectedRowKeys}`, 'selectedRows: ', selectedRows);
  },
  getCheckboxProps: record => ({
    disabled: record.name === 'Disabled User', // Column configuration not to be checked
    name: record.name,
  }),
};
```



### 指定每页的页数

在table控件中指定pagination

```jsx
<Table
  bordered
  dataSource={data.list}
  rowSelection={rowSelection}
  columns={this.columns}
  onChange={this.handleStandardTableChange}
  pagination={paginationProps}
/>
```

定义paginationprops

```jsx
const paginationProps = {
  showSizeChanger: true,
  showQuickJumper: true,
  ...pagination,
};
```



### 使用Ant Design Pro的Table控件

用法的区别

```jsx

<Table
  bordered
  dataSource={data.list}
  rowSelection={rowSelection}
  columns={this.columns}
  onChange={this.handleStandardTableChange}
  pagination={paginationProps}
/>
<StandardTable
  selectedRows={selectedRows}
  data={data}
  columns={this.columns}
  onSelectRow={this.handleSelectRows}
  onChange={this.handleStandardTableChange}
/>
```



* StandardTable 中多了 `selectedRows`与`onSelectRow`
* 少了：`rowSelection`,`paginationProps`

```jsx
state = {
  expandForm: false,
  formValues: {},
  selectedRows: [],
};

---------------------------------
    
const { selectedRows } = this.state;

---------------------------------

handleSelectRows = rows => {
  this.setState({
    selectedRows: rows,
  });
};    


```



#### 禁止选择某些项目

如果在记录中有disabled的记录，那么就禁止选择

```jsx
const rowSelection = {
  selectedRowKeys,
  onChange: this.handleRowSelectChange,
  getCheckboxProps: record => ({
    disabled: record.disabled,
  }),
};
```



## 编辑功能



###  新增功能



#### 定义一个弹出界面

使用了[Modal对话框](https://ant.design/components/modal-cn/)，官方文档中有介绍，提交服务器后，异步关闭对话框的功能。

这个对话框有4个输入项目：

* modalVisible 是否显示与关闭对话框
* form：在Form.create()得到的form对象。
* handleAdd：父窗口传入的回调函数，用来追加一条记录
  * 校验输入项目
  * 清空输入框
  * 调用父窗口的追加函数
* handleModalVisible：父窗口传入的回调函数，用来关闭窗口



```jsx
const CreateForm = Form.create()(props => {
  const { modalVisible, form, handleAdd, handleModalVisible } = props;
  const okHandle = () => {
    form.validateFields((err, fieldsValue) => {
      if (err) return;
      form.resetFields();
      handleAdd(fieldsValue);
    });
  };
  return (
    <Modal
      destroyOnClose
      title="新建规则"
      visible={modalVisible}
      onOk={okHandle}
      onCancel={() => handleModalVisible()}
    >
      <FormItem labelCol={{ span: 5 }} wrapperCol={{ span: 15 }} label="描述">
        {form.getFieldDecorator('desc', {
          rules: [{ required: true, message: '请输入至少五个字符的规则描述！', min: 5 }],
        })(<Input placeholder="请输入" />)}
      </FormItem>
    </Modal>
  );
});
```



#### 在父窗口中引用这个Form

这里定义了两个要传入的参数

```jsx
const parentMethods = {
  handleAdd: this.handleAdd,
  handleModalVisible: this.handleModalVisible,
};

<CreateForm {...parentMethods} modalVisible={modalVisible} />
```

定义两个函数

```jsx
handleModalVisible = flag => {
  this.setState({
    modalVisible: !!flag,
  });
};
handleAdd = fields => {
  const { dispatch } = this.props;
  dispatch({
    type: 'fanhlRule/add',
    payload: {
      desc: fields.desc,
    },
  });
  message.success('添加成功');
  this.handleModalVisible();
};
```



### 配置（多步编辑）功能



#### 定义一个form窗口

```jsx

@Form.create()
class UpdateForm extends PureComponent {
  static defaultProps = {
    handleUpdate: () => {},
    handleUpdateModalVisible: () => {},
    values: {},
  };

  constructor(props) {
    super(props);

    this.state = {
      formVals: {
        name: props.values.name,
        desc: props.values.desc,
        key: props.values.key,
        target: '0',
        template: '0',
        type: '1',
        time: '',
        frequency: 'month',
      },
      currentStep: 0,
    };

    this.formLayout = {
      labelCol: { span: 7 },
      wrapperCol: { span: 13 },
    };
  }

  handleNext = currentStep => {
    const { form, handleUpdate } = this.props;
    const { formVals: oldValue } = this.state;
    form.validateFields((err, fieldsValue) => {
      if (err) return;
      const formVals = { ...oldValue, ...fieldsValue };
      this.setState(
        {
          formVals,
        },
        () => {
          if (currentStep < 2) {
            this.forward();
          } else {
            handleUpdate(formVals);
          }
        }
      );
    });
  };

  backward = () => {
    const { currentStep } = this.state;
    this.setState({
      currentStep: currentStep - 1,
    });
  };

  forward = () => {
    const { currentStep } = this.state;
    this.setState({
      currentStep: currentStep + 1,
    });
  };

  renderContent = (currentStep, formVals) => {
    const { form } = this.props;
    if (currentStep === 1) {
      return [
        <FormItem key="target" {...this.formLayout} label="监控对象">
          {form.getFieldDecorator('target', {
            initialValue: formVals.target,
          })(
            <Select style={{ width: '100%' }}>
              <Option value="0">表一</Option>
              <Option value="1">表二</Option>
            </Select>
          )}
        </FormItem>,
        <FormItem key="template" {...this.formLayout} label="规则模板">
          {form.getFieldDecorator('template', {
            initialValue: formVals.template,
          })(
            <Select style={{ width: '100%' }}>
              <Option value="0">规则模板一</Option>
              <Option value="1">规则模板二</Option>
            </Select>
          )}
        </FormItem>,
        <FormItem key="type" {...this.formLayout} label="规则类型">
          {form.getFieldDecorator('type', {
            initialValue: formVals.type,
          })(
            <RadioGroup>
              <Radio value="0">强</Radio>
              <Radio value="1">弱</Radio>
            </RadioGroup>
          )}
        </FormItem>,
      ];
    }
    if (currentStep === 2) {
      return [
        <FormItem key="time" {...this.formLayout} label="开始时间">
          {form.getFieldDecorator('time', {
            rules: [{ required: true, message: '请选择开始时间！' }],
          })(
            <DatePicker
              style={{ width: '100%' }}
              showTime
              format="YYYY-MM-DD HH:mm:ss"
              placeholder="选择开始时间"
            />
          )}
        </FormItem>,
        <FormItem key="frequency" {...this.formLayout} label="调度周期">
          {form.getFieldDecorator('frequency', {
            initialValue: formVals.frequency,
          })(
            <Select style={{ width: '100%' }}>
              <Option value="month">月</Option>
              <Option value="week">周</Option>
            </Select>
          )}
        </FormItem>,
      ];
    }
    return [
      <FormItem key="name" {...this.formLayout} label="规则名称">
        {form.getFieldDecorator('name', {
          rules: [{ required: true, message: '请输入规则名称！' }],
          initialValue: formVals.name,
        })(<Input placeholder="请输入" />)}
      </FormItem>,
      <FormItem key="desc" {...this.formLayout} label="规则描述">
        {form.getFieldDecorator('desc', {
          rules: [{ required: true, message: '请输入至少五个字符的规则描述！', min: 5 }],
          initialValue: formVals.desc,
        })(<TextArea rows={4} placeholder="请输入至少五个字符" />)}
      </FormItem>,
    ];
  };

  renderFooter = currentStep => {
    const { handleUpdateModalVisible, values } = this.props;
    if (currentStep === 1) {
      return [
        <Button key="back" style={{ float: 'left' }} onClick={this.backward}>
          上一步
        </Button>,
        <Button key="cancel" onClick={() => handleUpdateModalVisible(false, values)}>
          取消
        </Button>,
        <Button key="forward" type="primary" onClick={() => this.handleNext(currentStep)}>
          下一步
        </Button>,
      ];
    }
    if (currentStep === 2) {
      return [
        <Button key="back" style={{ float: 'left' }} onClick={this.backward}>
          上一步
        </Button>,
        <Button key="cancel" onClick={() => handleUpdateModalVisible(false, values)}>
          取消
        </Button>,
        <Button key="submit" type="primary" onClick={() => this.handleNext(currentStep)}>
          完成
        </Button>,
      ];
    }
    return [
      <Button key="cancel" onClick={() => handleUpdateModalVisible(false, values)}>
        取消
      </Button>,
      <Button key="forward" type="primary" onClick={() => this.handleNext(currentStep)}>
        下一步
      </Button>,
    ];
  };

  render() {
    const { updateModalVisible, handleUpdateModalVisible, values } = this.props;
    const { currentStep, formVals } = this.state;

    return (
      <Modal
        width={640}
        bodyStyle={{ padding: '32px 40px 48px' }}
        destroyOnClose
        title="规则配置"
        visible={updateModalVisible}
        footer={this.renderFooter(currentStep)}
        onCancel={() => handleUpdateModalVisible(false, values)}
        afterClose={() => handleUpdateModalVisible()}
      >
        <Steps style={{ marginBottom: 28 }} size="small" current={currentStep}>
          <Step title="基本信息" />
          <Step title="配置规则属性" />
          <Step title="设定调度周期" />
        </Steps>
        {this.renderContent(currentStep, formVals)}
      </Modal>
    );
  }
}
```



#### 在父窗口引用这个功能

```jsx

const updateMethods = {
  handleUpdateModalVisible: this.handleUpdateModalVisible,
  handleUpdate: this.handleUpdate,
};


{stepFormValues && Object.keys(stepFormValues).length ? (
  <UpdateForm
    {...updateMethods}
    updateModalVisible={updateModalVisible}
    values={stepFormValues}
  />
) : null}
```



并且定义两个函数，并将函数传递到子窗口

```jsx
handleModalVisible = flag => {
  this.setState({
    modalVisible: !!flag,
  });
};

handleUpdate = fields => {
  const { dispatch } = this.props;
  const { formValues } = this.state;
  dispatch({
    type: 'fanhlRule/update',
    payload: {
      query: formValues,
      body: {
        name: fields.name,
        desc: fields.desc,
        key: fields.key,
      },
    },
  });
  message.success('配置成功');
  this.handleUpdateModalVisible();
};
```



### 添加批量操作

#### 添加相关按钮

如果有选择项目，就显示相关内容

```jsx
{selectedRows.length > 0 && (
  <span>
    <Button>批量操作</Button>
    <Dropdown overlay={menu}>
      <Button>
        更多操作 <Icon type="down" />
      </Button>
    </Dropdown>
  </span>
)}
```

制作一个下拉菜单，并调用相关函数

```jsx
const menu = (
  <Menu onClick={this.handleMenuClick} selectedKeys={[]}>
    <Menu.Item key="remove">删除</Menu.Item>
    <Menu.Item key="approval">批量审批</Menu.Item>
  </Menu>
);
```

#### 相关函数

这个地方有一个回调函数

```jsx
handleMenuClick = e => {
  const { dispatch } = this.props;
  const { selectedRows } = this.state;
  if (selectedRows.length === 0) return;
  switch (e.key) {
    case 'remove':
      dispatch({
        type: 'rule/remove',
        payload: {
          key: selectedRows.map(row => row.key),
        },
        callback: () => {
          this.setState({
            selectedRows: [],
          });
        },
      });
      break;
    default:
      break;
  }
};
```



### dispatch 异步的回调函数

在这个可以判断与服务器的交互是否成功

#### 在UI界面上，要给出callback回调函数

```jsx
dispatch({
  type: 'fanhlRule/remove',
  payload: {
    key: selectedRows.map(ro
  },
  callback: (res) => {
    console.log(res)
    this.setState({
      selectedRows: [],
    });
  },
});
```



#### 在model类中，回调这个函数

```jsx
*remove({ payload, callback }, { call, put }) {
  const response = yield call(removeRule, payload);
  yield put({
    type: 'save',
    payload: response,
  });
  if (callback) callback(response);
},
```

