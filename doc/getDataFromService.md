# 如何访问后台服务器



https://gitee.com/springcloud_ourslook/ant-design-pro



## 基本功能设计

* 检索功能：
  * 从服务器得到分页数据。
  * 排序功能(今后再做)
  * 筛选功能(今后再做)
* 增加功能：
  * Post一个JSON串
  * 上传文件(今后再做)
* 编辑功能
* 删除功能
* 多个选择功能





## API接口

> 测试地址

https://wx.runzhichina.com/wechat/adImgs

| 地址           | 功能       | 参数                                                         |
| -------------- | ---------- | ------------------------------------------------------------ |
| /imgList       | 得到全部   |                                                              |
| /add           | 追加       | @RequestBody AdImg adImg                                     |
| /update        | 更新       | @RequestBody AdImg adImg                                     |
| /delete        | 删除       | @RequestParam Long adImgId                                   |
| /selectById    | 按照id查询 | @RequestParam Long adImgId                                   |
| /getListByPage | 分页检索   | @RequestParam Integer pageNum,@RequestParam  Integer pageSize |



```java
public class AdImg implements Serializable {
    private Long adImgId;

    private String fileName;

    private String imgColor;

    private String imgType;

    private String description;

    private Integer status;

    private Date gmtCreate;

    private Date gmtModified;

    private static final long serialVersionUID = 1L;

}
```



> 其他

*  [JSONPlaceholder - 免费的在线REST服务](http://www.hangge.com/blog/cache/detail_2020.html)

> 相关文档

https://ant.design/components/table-cn/#components-table-demo-ajax

http://localhost:8000/fanhl/tableList1

http://localhost:8000/list/card-list



## Service

* `stringify`可以将一个对象转换成一个字符串

```jsx
import { stringify } from 'qs';

const serverUrl = 'https://wx.runzhichina.com/wechat/adImgs';

export async function getAdImgList() {
  return request(`${serverUrl}/imgList`);
}

export async function addAdImg(payload) {
  return request(`${serverUrl}/add`, {
    method: 'POST',
    data: {
      ...payload
    },
  });
}

export async function updateAdImg(payload) {
  return request(`${serverUrl}/update`, {
    method: 'POST',
    data: {
      ...payload
    },
  });
}

export async function deleteAdImg(payload) {
  return request(`${serverUrl}/delete?${stringify(payload)}`);
}

export async function selectAdImgById(adImgId) {
  return request(`${serverUrl}/selectById?adImgId=${adImgId}`);
}

export async function getAdImgListByPage(payload) {
  const url = `${serverUrl}/getListByPage?${stringify(payload)}`;
  return request(url);
}
```



## Model

定义了state中的list与pagination

这里面有几个数组操作：

* 在数组中追加：newArr.push(...list)
* 在数组中删除：const list=state.data.list.filter(item=>item.adImgId!==adImgId)
* 在数组中更新：list[i]=Object.assign(item, action.payload); `Object.assign` 复制对象函数

```jsx
/* eslint-disable no-unused-vars */
import {
  getAdImgList,
  addAdImg,
  updateAdImg,
  deleteAdImg,
  getAdImgListByPage,
  selectAdImgById,
} from '@/services/new';

export default {
  namespace: 'adImg',

  state: {
    data: {
      list: [],
      pagination: {},
    },
  },

  effects: {
    *getAdImgList({ payload }, { call, put }) {
      const response = yield call(getAdImgList, payload);
      yield put({
        type: 'select',
        payload: response,
      });
    },
    *getAdImgListByPage({ payload }, { call, put }) {
      const response = yield call(getAdImgListByPage, payload);
      yield put({
        type: 'select',
        payload: response,
      });
    },
    *addAdImg({ payload }, { call, put }) {
      const response = yield call(addAdImg, payload);
      const newRecorder = yield call(selectAdImgById, response);

      yield put({
        type: 'add',
        payload: {...newRecorder},
      });
    },
    *updateAdImg({ payload }, { call, put }) {
      const response = yield call(updateAdImg, payload);
      console.log(response);
      yield put({
        type: 'update',
        payload,
      });
    },
    *deleteAdImg({ payload }, { call, put }) {
      const response = yield call(deleteAdImg, payload);
      console.log(response);
      yield put({
        type: 'delete',
        payload,
      });
    },


  },

  reducers: {
    select(state, action) {
      return {
        ...state,
        data: action.payload,
      };
    },

    add(state, action) {
      const newArr=[{...action.payload,newItem:true,}];
      const {list}=state.data;
      newArr.push(...list)
      const{pagination}=state.data;
      console.log(newArr)
      return {
        data:{
          list:newArr,
          pagination,
        }
      };
    },

    update(state, action) {
      const {list}=state.data;
      const {adImgId}=action.payload;
      list.forEach((item,i)=>{
        if(item.adImgId===adImgId){
          list[i]=Object.assign(item, action.payload);
        }
      });
      const{pagination}=state.data;
      return {
        data:{
          list,
          pagination,
        }
      };
    },

    delete(state, action) {
      const{adImgId}=action.payload;
      const list=state.data.list.filter(item=>item.adImgId!==adImgId)
      const{pagination}=state.data;
      return {
        data:{
          list,
          pagination,
        }
      };
    },
  },
};
```



## TableList界面

注意事项：

* `rowKey={record => record.adImgId}`为Table指定一个key
* `pagination={paginationProps}` 当为false时，不显示分页控件
* `const {adImg:{data:{list,pagination}},loading}=this.props`将属性显示出来
  * `const {adImg:newArg}=this.props`将props中的const赋值给newArg变量。



```jsx
import React, { Fragment, PureComponent } from 'react';
import { Button, Card, Divider, Modal, Table } from 'antd';
import { connect } from 'dva';
import TableList2Modal from './TableList2Modal';
import styles from '../List/TableList.less';

@connect(({ adImg, loading }) => ({
  adImg,
  loading: loading.models.rule,
}))
class TableList extends PureComponent {
  state = {
    editFormVisible: false,
    editFormValues: {},
  };

  columns = [
    {
      title: '编号',
      dataIndex: 'adImgId',
    },
    {
      title: '图片名',
      dataIndex: 'fileName',
      width: '20%',
    },
    {
      title: '描述',
      dataIndex: 'description',
    },
    {
      title: '操作',
      render: (text, record) => (
        <Fragment>
          <a onClick={() => this.handleEditModalVisible(true, record)}>编辑</a>
          <Divider type="vertical" />
          <a onClick={() => this.handleDelete(record)}>删除 </a>
        </Fragment>
      ),
    },
  ];

  componentDidMount() {
    this.getAdImgListByPage(1, 3);
  }

  getAdImgListByPage(pageNum, pageSize) {
    const { dispatch } = this.props;
    dispatch({
      type: 'adImg/getAdImgListByPage',
      payload: {
        pageNum,
        pageSize,
      },
    });
  }

  /* eslint-disable no-unused-vars */
  handleTableChange = (pagination, filters, sorter) => {
    const { current, pageSize } = pagination;
    this.getAdImgListByPage(current, pageSize);
  };

  handleSave=(record)=>{
    const { dispatch } = this.props;
    if(record.adImgId===undefined){
      // 追加数据
      dispatch({
        type: 'adImg/addAdImg',
        payload: {
          ...record
        },
      });
    }else{
      // 更新记录
      dispatch({
        type: 'adImg/updateAdImg',
        payload: {
          ...record
        },
      });
    }
    this.handleEditModalVisible(false,{});
  }

  deleteItem=(adImgId)=>{
    const { dispatch } = this.props;
    dispatch({
      type: 'adImg/deleteAdImg',
      payload: {
        adImgId
      },
    });
  }

  handleDelete=(record)=>{
    Modal.confirm({
      title: '删除记录',
      content: '确定删除该记录吗？',
      okText: '确认',
      cancelText: '取消',
      onOk: () => this.deleteItem(record.adImgId),
    });
  }

  handleEditModalVisible=(flag,record)=>{
    this.setState({
      editFormVisible:flag,
      editFormValues:record,
    })
  }

  render() {
    const {
      adImg: {
        data: { list, pagination },
      },
      loading,
    } = this.props;
    const{editFormVisible,editFormValues}=this.state;
    const paginationProps = {
      showSizeChanger: true,
      showQuickJumper: true,
      pageSizeOptions: ['3', '5', '10', '20', '40'],
      ...pagination,
    };
    const tableHandlMethods = {
      handleEditModalVisible: this.handleEditModalVisible,
      handleSave: this.handleSave,
    };

    const backAndTextColor = {
      backgroundColor:'red',
    };

    return (
      <Fragment>
        <Card bordered={false}>
          <div className={styles.tableListOperator}>
            <Button icon="plus" type="primary" onClick={() => this.handleEditModalVisible(true,{})}>
              新建
            </Button>
          </div>
          <Table
            columns={this.columns}
            rowKey={record => record.adImgId}
            dataSource={list}
            pagination={paginationProps}
            onChange={this.handleTableChange}
            loading={loading}
          />
        </Card>
        <TableList2Modal
          {...tableHandlMethods}
          editFormVisible={editFormVisible}
          editFormValues={editFormValues}
        />
      </Fragment>
    );
  }
}

export default TableList;
```



> 如果JSON内容用属性与变量相同，那么就用简写，例如`payload`

```jsx
getAdImgListByPage(pageNum, pageSize) {
  const { dispatch } = this.props;
  dispatch({
    type: 'adImg/getAdImgListByPage',
    payload: {
      pageNum,
      pageSize,
    },
  });
```



##  对话框设计思路



### 数据结构

```json
{"adImgId":1,"fileName":"001.jpg","imgColor":"868f8a","imgType":null,"description":"了解腹部肌肉的构成，这能使你的训练效果达到最佳","status":0,"gmtCreate":"2018-08-11 21:09:43","gmtModified":"2018-08-11 21:09:43"}
```



### 父窗口设计



#### 控制对话框的State

```jsx
state = {
  editFormVisible: false,
  editFormValues: {},
};
```



#### 对话框的保存与关闭回调函数

```jsx
handleSave=(record)=>{
  this.handleEditModalVisible(false,record);
}
handleEditModalVisible=(flag,record)=>{
  this.setState({
    editFormVisible:flag,
    editFormValues:record,
  })
}
```



#### 引用弹出组件

```jsx
const tableHandlMethods = {
  handleEditModalVisible: this.handleEditModalVisible,
  handleSave: this.handleSave,
};

<TableList2Modal
  {...tableHandlMethods}
  editFormVisible={editFormVisible}
  editFormValues={editFormValues}
/>
```



#### 在表格控件上添加编辑与删除链接

```jsx
{
  title: '操作',
  render: (text, record) => (
    <Fragment>
      <a onClick={() => this.handleEditModalVisible(true, record)}>编辑</a>
      <Divider type="vertical" />
      <a href="">删除</a>
    </Fragment>
  ),
},
const tableHandlMethods = {
  handleEditModalVisible: this.handleEditModalVisible,
  handleSave: this.handleSave,
};
```







##  对话框代码

* 定义一个EditForm

  * 输入：

    * 是否显示 ：editFormVisible
    * 要编辑记录：editFormValues
    * 回调函数：
      * 控制窗口关闭与显示：handleEditModalVisible
      * 控制保存函数：handleSave，根据情况来确定是insert还是update

  * 定义一个Form

    * 外层是一个：`Modal`对话框

    * `getModalContent`用来得到Form的内容

    * 两个按钮函数

      * okHandle：判断输入的是否正确后，调用父窗口函数
      * onCancel：直接调用父窗口的函数

      

    ```jsx
    import React ,{ Fragment } from 'react';
    import { Form, Input, Modal,Switch } from 'antd';
    
    const FormItem = Form.Item;
    const { TextArea } = Input;
    
    const EditForm = Form.create()(props => {
      const {form, editFormVisible,editFormValues,handleEditModalVisible,handleSave} = props;
      const {
        form: { getFieldDecorator },
      } = props;
    
      const formLayout = {
        labelCol: { span: 7 },
        wrapperCol: { span: 13 },
      };
    
      const okHandle = () => {
        form.validateFields((err, fieldsValue) => {
          if (err) return;
          form.resetFields();
          const newValues={
            ...editFormValues,...fieldsValue
          };
          newValues.status= newValues.status?0:1;
          handleSave(newValues);
        });
      };
    
      const getModalContent = () => {
        let status=false;
        if( editFormValues.status === undefined ||  editFormValues.status === 0 ) {
          status = true;
        }
        return(
          <Fragment>
            <FormItem label="文件名" {...formLayout}>
              {getFieldDecorator('fileName', {
                rules: [{ required: true, message: '文件名' }],
                initialValue: editFormValues.fileName!==undefined ? editFormValues.fileName : null,
              })(<Input placeholder="请输入" />)}
            </FormItem>
            <FormItem label="背景颜色" {...formLayout}>
              {getFieldDecorator('imgColor', {
                rules: [{ required: true, message: '背景颜色' }],
                initialValue: editFormValues.imgColor!==undefined ? editFormValues.imgColor : null,
              })(<Input placeholder="请输入" />)}
            </FormItem>
            <FormItem label="是否可用" {...formLayout}>
              {getFieldDecorator('status', {
                rules: [{ required: true, message: '是否可用' }],
                initialValue: status,
              })(<Switch checkedChildren="可用" unCheckedChildren="禁用" defaultChecked={status} />)}
            </FormItem>
            <FormItem label="描述" {...formLayout}>
              {getFieldDecorator('description', {
                rules: [{ required: true, message: '描述' }],
                initialValue: editFormValues.description!==undefined ? editFormValues.description : null,
              })(<TextArea rows={4} placeholder="请输入至少五个字符" />)}
            </FormItem>
          </Fragment>
        )
      };
      // console.log(editFormValues)
      return (
    
        <Modal
          width={640}
          bodyStyle={{ padding: '32px 40px 48px' }}
          destroyOnClose
          title={editFormValues.adImgId ? '编辑图片' : '添加图片'}
          visible={editFormVisible}
          onOk={okHandle}
          onCancel={() => handleEditModalVisible(false,{})}
        >
          {getModalContent()}
        </Modal>
      )
    })
    
    export default EditForm;
    ```

    





