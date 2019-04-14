# React 官方内容学习笔记

本笔记是在Ant Design Pro与[React官方网站](https://zh-hans.reactjs.org)的学习内容。



## React 哲学-页面开发思路

[原文](https://zh-hans.reactjs.org/docs/thinking-in-react.html)

* 从设计稿开始
  * 假设我们已经有了一个返回 JSON 的 API
* 第一步：将设计好的 UI 划分为组件层级
* 第二步：用 React 创建一个静态版本
  * 补充说明: 有关 props 和 state
* 第三步：确定 UI state 的最小（且完整）表示
* 第四步：确定 state 放置的位置
* 第五步：添加反向数据流



参考例子：

### 第一步：将设计好的 UI 划分为组件层级

* FilterableProductTable
  * SearchBar
  * ProductTable

    * ProductCategoryRow
    * ProductRow

### 第二步：用 React 创建一个静态版本

最容易的方式，是先用已有的数据模型渲染一个不包含交互功能的 UI。最好将渲染 UI 和添加交互这两个过程分开。这是因为，编写一个应用的静态版本时，往往要编写大量代码，而不需要考虑太多交互细节；添加交互功能时则要考虑大量细节，而不需要编写太多代码。所以，将这两个过程分开进行更为合适。



### 第三步：确定 UI state 的最小（且完整）表示

我们的示例应用拥有如下数据：
* 包含所有产品的原始列表
* 用户输入的搜索词
* 复选框是否选中的值
* 经过搜索筛选的产品列表



**通过问自己以下三个问题，你可以逐个检查相应数据是否属于 state：**

1. 该数据是否是由父组件通过 props 传递而来的？如果是，那它应该不是 state。

2. 该数据是否随时间的推移而保持不变？如果是，那它应该也不是 state。

3. 你能否根据其他 state 或 props 计算出该数据的值？如果是，那它也不是 state。

   

包含`所有产品的原始列表`是经由 props 传入的，所以它不是 state；

`搜索词`和`复选框的值`应该是 state，因为它们随时间会发生改变且无法由其他数据计算而来；

`经过搜索筛选的产品列表`不是 state，因为它的结果可以由产品的原始列表根据搜索词和复选框的选择计算出来。



综上所述，属于 state 的有：

- [x] 用户输入的搜索词
- [x] 复选框是否选中的值




### 第四步：确定 state 放置的位置

我们需要确定哪个组件能够改变这些 state，或者说*拥有*这些 state。

具体步骤：

- 找到根据这个 state 进行渲染的所有组件。
- 找到他们的共同所有者（common owner）组件（在组件层级上高于所有需要该 state 的组件）。
- 该共同所有者组件或者比它层级更高的组件应该拥有该 state。
- 如果你找不到一个合适的位置来存放该 state，就可以直接创建一个新的组件来存放该 state，并将这一新组件置于高于共同所有者组件层级的位置。



### 第五步：添加反向数据流

到目前为止，我们已经借助自上而下传递的 props 和 state 渲染了一个应用。现在，我们将尝试让数据反向传递：处于较低层级的表单组件更新较高层级的 `FilterableProductTable` 中的 state。

React 通过一种比传统的双向绑定略微繁琐的方法来实现反向数据传递。尽管如此，但这种需要显式声明的方法更利于人们理解程序的运作方式。

如果你在这时尝试在搜索框输入或勾选复选框，React 不会产生任何响应。这是正常的，因为我们之前已经将 `input` 的值设置为了从 `FilterableProductTable` 的 `state` 传递而来的固定值。

让我们重新梳理一下需要实现的功能：每当用户改变表单的值，我们需要改变 state 来反映用户的当前输入。由于 state 只能由拥有它们的组件进行更改，`FilterableProductTable` 必须将一个能够触发 state 改变的回调函数（callback）传递给 `SearchBar`。我们可以使用输入框的 `onChange` 事件来监视用户输入的变化，并通知 `FilterableProductTable` 传递给 `SearchBar`的回调函数。然后该回调函数将调用 `setState()`，从而更新应用。



### 实例代码

由于Ant Design Pro 启动了eslint代码检查，多个React Componet不能放入到一个文件中，所以拆成了多个文件。



> SearchBar 组件代码

```jsx
import React from 'react';

class SearchBar extends React.Component {
  constructor(props) {
    super(props);
    this.handleFilterTextChange = this.handleFilterTextChange.bind(this);
    this.handleInStockChange = this.handleInStockChange.bind(this);
  }

  handleFilterTextChange(e) {
    const  {onFilterTextChange}=this.props;
    onFilterTextChange(e.target.value);
  }

  handleInStockChange(e) {
    const  {onInStockChange}=this.props;
    onInStockChange(e.target.checked);
  }

  render() {
    const {filterText} = this.props;
    const {inStockOnly} = this.props;
    return (
      <form>
        <input type="text" placeholder="Search..." value={filterText} onChange={this.handleFilterTextChange} />
        <p>
          <input type="checkbox" checked={inStockOnly} onChange={this.handleInStockChange} />
          {' '}
          Only show products in stock
        </p>
      </form>
    );
  }
}

export default SearchBar;
```

> Page代码

```jsx
import React from 'react';
import SearchBar from './Pro-sub';

function ProductCategoryRow(props) {
  const {category} = props;
  return (
    <tr>
      <th colSpan="2">
        {category}
      </th>
    </tr>
  );
}

function ProductRow(props) {
  const { product } = props;
  const name = product.stocked ?
    product.name :
    <span style={{ color: 'red' }}>
      {product.name}
    </span>;

  return (
    <tr>
      <td>{name}</td>
      <td>{product.price}</td>
    </tr>
  );
}

function ProductTable (props) {
    const {filterText} = props;
    const {inStockOnly} = props;
    const rows = [];
    let lastCategory = null;
    const {products}=props;

    products.forEach((product) => {
      if (product.name.indexOf(filterText) === -1) {
        return;
      }
      if (inStockOnly && !product.stocked) {
        return;
      }
        if (product.category !== lastCategory) {
        rows.push(
          <ProductCategoryRow
            category={product.category}
            key={product.category}
          />
        );
      }
      rows.push(
        <ProductRow
          product={product}
          key={product.name}
        />
      );
      lastCategory = product.category;
    });

    return (
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Price</th>
          </tr>
        </thead>
        <tbody>{rows}</tbody>
      </table>
    );
}


class FilterableProductTable extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      filterText: 'ball',
      inStockOnly: false
    };
    this.handleFilterTextChange = this.handleFilterTextChange.bind(this);
    this.handleInStockChange = this.handleInStockChange.bind(this);
  }

  handleFilterTextChange(filterText1) {
    this.setState({
      filterText: filterText1
    });
  }

  handleInStockChange(inStockOnly1) {
    this.setState({
      inStockOnly: inStockOnly1
    })
  }

  render() {
    const {products}=this.props;
    const {filterText,inStockOnly}=this.state;
    return (
      <div>
        <SearchBar
          filterText={filterText}
          inStockOnly={inStockOnly}
          onFilterTextChange={this.handleFilterTextChange}
          onInStockChange={this.handleInStockChange}
        />
        <ProductTable
          products={products}
          filterText={filterText}
          inStockOnly={inStockOnly}
        />
      </div>
    );
  }
}

const PRODUCTS = [
  {category: 'Sporting Goods', price: '$49.99', stocked: true, name: 'Football'},
  {category: 'Sporting Goods', price: '$9.99', stocked: true, name: 'Baseball'},
  {category: 'Sporting Goods', price: '$29.99', stocked: false, name: 'Basketball'},
  {category: 'Electronics', price: '$99.99', stocked: true, name: 'iPod Touch'},
  {category: 'Electronics', price: '$399.99', stocked: false, name: 'iPhone 5'},
  {category: 'Electronics', price: '$199.99', stocked: true, name: 'Nexus 7'}
];

function Page() {
  return (
    <FilterableProductTable products={PRODUCTS} />
  );
}

export default Page;
```



## Context

[官方文档地址](https://zh-hans.reactjs.org/docs/context.html)



Context 提供了一个无需为每层组件手动添加 props，就能在组件树间进行数据传递的方法。

在一个典型的 React 应用中，数据是通过 props 属性自上而下（由父及子）进行传递的，但这种做法对于某些类型的属性而言是极其繁琐的（例如：地区偏好，UI 主题），这些属性是应用程序中许多组件都需要的。Context 提供了一种在组件之间共享此类值的方式，而不必显式地通过组件树的逐层传递 props。

### 何时使用 Context

Context 设计目的是为了共享那些对于一个组件树而言是“全局”的数据，例如当前认证的用户、主题或首选语言。举个例子，在下面的代码中，我们通过一个 “theme” 属性手动调整一个按钮组件的样式：

```jsx
// Context 可以让我们无须明确地传遍每一个组件，就能将值深入传递进组件树。
// 为当前的 theme 创建一个 context（“light”为默认值）。
const ThemeContext = React.createContext('light');

class App extends React.Component {
  render() {
    // 使用一个 Provider 来将当前的 theme 传递给以下的组件树。
    // 无论多深，任何组件都能读取这个值。
    // 在这个例子中，我们将 “dark” 作为当前的值传递下去。
    return (
      <ThemeContext.Provider value="dark">
        <Toolbar />
      </ThemeContext.Provider>
    );
  }
}

// 中间的组件再也不必指明往下传递 theme 了。
function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

class ThemedButton extends React.Component {
  // 指定 contextType 读取当前的 theme context。
  // React 会往上找到最近的 theme Provider，然后使用它的值。
  // 在这个例子中，当前的 theme 值为 “dark”。
  static contextType = ThemeContext;
  render() {
    return <Button theme={this.context} />;
  }
}
```



### 使用 Context 之前的考虑

Context 主要应用场景在于*很多*不同层级的组件需要访问同样一些的数据。请谨慎使用，因为这会使得组件的复用性变差。

**如果你只是想避免层层传递一些属性，组件组合（component composition）有时候是一个比 context 更好的解决方案。**





## Refs



### 何时使用 Refs

下面是几个适合使用 refs 的情况：

- 管理焦点，文本选择或媒体播放。
- 触发强制动画。
- 集成第三方 DOM 库。

避免使用 refs 来做任何可以通过声明式实现来完成的事情。

举个例子，避免在 `Dialog` 组件里暴露 `open()` 和 `close()` 方法，最好传递 `isOpen` 属性。



##  Hook 简介

> 什么是Hook

官方文档

https://zh-hans.reactjs.org/docs/hooks-overview.html



```jsx
import React, { useState } from 'react';

function Example() {
  // 声明一个新的叫做 “count” 的 state 变量
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>You clicked {count} times</p>
      <button type='button' onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
export default Example;
```



### 引入Hook的原因



####  在组件之间复用状态逻辑很难



### Hook 使用规则

Hook 就是 JavaScript 函数，但是使用它们会有两个额外的规则：

- 只能在**函数最外层**调用 Hook。不要在循环、条件判断或者子函数中调用。
- 只能在 **React 的函数组件**中调用 Hook。不要在其他 JavaScript 函数中调用。（还有一个地方可以调用 Hook —— 就是自定义的 Hook 中，我们稍后会学习到。）



### 自定义 Hook



## 使用Chrome插件来Debug

[最简单的安装React Devtools调试工具](https://blog.csdn.net/one_girl/article/details/80916232)











