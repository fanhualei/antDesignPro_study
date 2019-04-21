# ant design pro 例子 - 异常页面

> 目录

* [异常控件使用](#异常控件使用)
* [全局异常控制](#全局异常控制)



## 异常控件使用

[官方文档](https://pro.ant.design/components/Exception-cn/)

主要的代码

```js
import React from 'react';
import { formatMessage } from 'umi/locale';
import Link from 'umi/link';
import Exception from '@/components/Exception';

const Exception403 = () => (

  <div>
  <Exception
    type="403"
    desc={formatMessage({ id: 'app.exception.description.403' })}
    linkElement={Link}
    backText={formatMessage({ id: 'app.exception.back' })}
  />

  <Link to="/Exception/trigger">go to /Exception/trigger</Link>
  </div>
);

export default Exception403;
```



## 全局异常控制

这个要学习一下



