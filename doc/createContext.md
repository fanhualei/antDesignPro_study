# Context

Context 提供了一个无需为每层组件手动添加 props，就能在组件树间进行数据传递的方法。

在一个典型的 React 应用中，数据是通过 props 属性自上而下（由父及子）进行传递的，但这种做法对于某些类型的属性而言是极其繁琐的（例如：地区偏好，UI 主题），这些属性是应用程序中许多组件都需要的。Context 提供了一种在组件之间共享此类值的方式，而不必显式地通过组件树的逐层传递 props。



[官方说明](https://zh-hans.reactjs.org/docs/context.html)

下面是我写的一个例子：

```jsx
import React, { PureComponent } from 'react';

const { Provider,Consumer } = React.createContext();

function Parent (props) {
  return (
    <div>
      <div>Parent: </div>
      <Son />
    </div>
  )
}

// 子组件
function Son (props) {
  return (
    <div>
      <div>Son: </div>
      <Child />
    </div>

  )
}
// 孙子组件
function Child (props) {
  return (
    <Consumer>
      {value =>
        <div>
          value: {value}
        </div>
      }
    </Consumer>
  )
}


class BasicForms extends PureComponent {
  render() {
    return(
      <div>
        <Provider value="1">
          <Parent />
        </Provider>
      </div>
    )
  }
}


export default BasicForms;
```

