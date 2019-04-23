# 如何访问后台服务器



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

const serverUrl='https://wx.runzhichina.com/wechat/adImgs';

export async function getAdImgList() {
  return request( `${serverUrl}/imgList`);
}

export async function addAdImg() {
  return request( `${serverUrl}/add`);
}

export async function updateAdImg() {
  return request( `${serverUrl}/update`);
}

export async function deleteAdImg() {
  return request( `${serverUrl}/delete`);
}

export async function getAdImgListByPage(payload) {
  const url=`${serverUrl}/getListByPage?${stringify(payload)}`;
  return request( url);
}
```



## Model

定义了state中的list与pagination

```jsx
/* eslint-disable no-unused-vars */
import {getAdImgList,addAdImg,updateAdImg,deleteAdImg,getAdImgListByPage } from '@/services/new';

export default {
  namespace: 'adImg',

  state: {
    data: {
      list: [],
      pagination: {},
    },
  },

  effects: {
    *getAdImgList({payload},{call,put}){
      const response=yield call(getAdImgList,payload);
      yield put({
        type:'save',
        payload:response,
      });
    },
    *getAdImgListByPage({payload},{call,put}){
      const response=yield call(getAdImgListByPage,payload);
      yield put({
        type:'save',
        payload:response,
      });
    },
  },

  reducers:{
    save(state,action){
      return{
        ...state,
        data:action.payload,
      }
    }
  }

}
```



## 组件

注意事项：

* `rowKey={record => record.adImgId}`为Table指定一个key
* `pagination={paginationProps}` 当为false时，不显示分页控件
* `const {adImg:{data:{list,pagination}},loading}=this.props`将属性显示出来
  * `const {adImg:newArg}=this.props`将props中的const赋值给newArg变量。



```jsx
import React, { Fragment, PureComponent } from 'react';
import { Table } from 'antd';
import { connect } from 'dva';

const columns = [{
  title: '编号',
  dataIndex: 'adImgId',
}, {
  title: '图片名',
  dataIndex: 'fileName',
  width: '20%',
}, {
  title: '描述',
  dataIndex: 'description',
}];
@connect(({ adImg, loading }) => ({
  adImg,
  loading: loading.models.rule,
}))
class TableList extends PureComponent {
  componentDidMount() {
    this.getAdImgListByPage(1,3);
  }

  getAdImgListByPage(pageNum,pageSize){
    const { dispatch } = this.props;
    dispatch({
      type: 'adImg/getAdImgListByPage',
      payload:{
        'pageNum':pageNum,
        'pageSize':pageSize,
      },
    });
  }

  /* eslint-disable no-unused-vars */
  handleTableChange = (pagination, filters, sorter) => {
    const {current,pageSize}=pagination
    this.getAdImgListByPage(current,pageSize);
  }

  render() {
    const {adImg:{data:{list,pagination}},loading}=this.props
    const paginationProps = {
      showSizeChanger: true,
      showQuickJumper: true,
      pageSizeOptions:['3','5','10','20','40'],
      ...pagination,
    };
    return(
      <Fragment>
        <Table
          columns={columns}
          rowKey={record => record.adImgId}
          dataSource={list}
          pagination={paginationProps}
          onChange={this.handleTableChange}
          loading={loading}
        />
      </Fragment>
    )
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





