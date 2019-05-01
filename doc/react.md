# React 基数语法笔记

下面文档是在[菜鸟React基本语法基础上](http://www.runoob.com/react/react-tutorial.html)+Ant Design Pro 框架基础上的笔记

[TOC]









## React 组件

每个函数都是一个组件，并且可以接受父亲组件传递过来的数据

>  展示结果

![alt](imgs\react-1.png)



> 代码

```jsx
import React from 'react';

// 这是一个组件
function HelloMessage(props) {
  const {name} = props;
  return (
    <div>
      <div>下面是react组件例子</div>
      <h4>Hello {name}!</h4>;
    </div>
  )
}
// 默认 Props
HelloMessage.defaultProps = {
  name: 'Runoob'
};

function ReactExample1() {
  const i = 0;
  return (
    <div>
      <h3>React 组件例子，以及基本语法 </h3>
      <div>{i === 1 ? 'True!' : 'False'}</div>
      <div>{1 + 1}</div>
      <HelloMessage name="小千" />
      <HelloMessage />
    </div>
  );
}
export default ReactExample1;
```



## React State(状态)

React 里，只需更新组件的 state，然后根据新的 state 重新渲染用户界面（不要操作 DOM）。

* 主要功能点
  * constructor构造函数中定义state
  * 将生命周期方法添加到类中
  * 定时器的应用
  * 数据自顶向下流动，state传递到子组件中



> 显示结果

![alt](imgs\react-2.png)



> 代码

```jsx
import React from 'react';

// 这是一个组件，用来演示state可以传递到子组件中
function FormattedDate(props) {
  const {date}= props;
  return <h2>现在是 {date.toLocaleTimeString()}.</h2>;
}


class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      date: new Date(),
      title: "动态时间显示"
    };
  };

  // 在组件输出到 DOM 后会执行 componentDidMount()
  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  };

  // 组件卸载 this.timerID 为定时器的 ID
  componentWillUnmount() {
    clearInterval(this.timerID);
  };

  // 设置state的函数
  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    const {date, title}=this.state;
    return (
      <div>
        <h3>React State(状态) 例子：{title}</h3>
        <FormattedDate date={date} />
        <FormattedDate date={date} />
      </div>
    );
  };
}

export default Clock;

```



## React Props

state 和 props 主要的区别在于 **props** 是不可变的，而 state 可以根据与用户交互来改变。这就是为什么有些容器组件需要定义 state 来更新和修改数据。 而子组件只能通过 props 来传递数据。

> 例如这个组件里面传递了一个props，并设定默认的 Props

```jsx
// 这是一个组件
function HelloMessage(props) {
  const {name} = props;
  return (
    <div>
      <div>下面是react组件例子</div>
      <h4>Hello {name}!</h4>;
    </div>
  )
}
// 默认 Props
HelloMessage.defaultProps = {
  name: 'Runoob'
};
```



### Props 验证

Props 验证使用 **propTypes**，它可以保证我们的应用组件被正确使用，React.PropTypes 提供很多验证器 (validator) 来验证传入数据是否有效。当向 props 传入无效数据时，JavaScript 控制台会抛出警告。

[更多说明](http://www.runoob.com/react/react-props.html)



## React 事件处理



### 语法区别

React 元素的事件处理和 DOM 元素类似。但是有一点语法上的不同:

- React 事件绑定属性的命名采用驼峰式写法，而不是小写。
- 如果采用 JSX 的语法你需要传入一个函数作为事件处理函数，而不是一个字符串(DOM 元素的写法)

HTML 通常写法是：

```html
<button onclick="activateLasers()">
  激活按钮
</button>
```

React 中写法为：

```jsx
<button onClick={activateLasers}>
  激活按钮
</button>
```



在 React 中另一个不同，是你不能使用返回 **false** 的方式阻止默认行为， 你必须明确的使用 **preventDefault**。

例如，通常我们在 HTML 中阻止链接默认打开一个新页面，可以这样写：

```html
<a href="#" onclick="console.log('点击链接'); return false">
  点我
</a>
```

在 React 的写法为：

```jsx
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('链接被点击');
  }
 
  return (
    <a href="#" onClick={handleClick}>
      点我
    </a>
  );
}
```

实例中 e 是一个合成事件。



### DOM 元素监听器

使用 React 的时候通常你不需要使用 addEventListener 为一个已创建的 DOM 元素添加监听器。你仅仅需要在这个元素初始渲染的时候提供一个监听器。

* handleClick1 是一种简便的写法，这种性能稍微低一点

* 向事件处理程序传递参数preventPop



事件：**this.handleclick.bind(this，要传的参数)**

函数：**handleclick(传过来的参数，event)**



```jsx
import React from 'react';

class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = { isToggleOn: true };
    // 这边绑定是必要的，这样 `this` 才能在回调函数中使用
    this.handleClick = this.handleClick.bind(this);
  }

  // 下面这种做法不用绑定回调函数
  handleClick1 = () => {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn,
    }));
  };

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn,
    }));
  };

  // 事件对象e要放在最后
  preventPop(name, age, event) {
    event.preventDefault();
    alert(name + age);
  };

  render() {
    const { isToggleOn } = this.state;
    return (
      <div>
        <div>
          演示事件处理器：
          <button type="button" onClick={this.handleClick}>
            {isToggleOn ? 'ON' : 'OFF'}
          </button>
          <button type="button" onClick={this.handleClick1}>
            {isToggleOn ? 'ON' : 'OFF'}
          </button>
        </div>
        <div>
          演示传递参数：
          <button type="button" onClick={this.preventPop.bind(this,'你好呀', 16)}>
            弹出信息
          </button>
        </div>
      </div>
    );
  }
}
export default Toggle;

```

## React 条件渲染

[官方文档](http://www.runoob.com/react/react-conditional-rendering.html)

* 可以通过If语句来判断显示什么内容。
* JavaScript 的逻辑与 &&，它可以方便地条件渲染一个元素。
* 通过三目运算符：condition ? true : false。
* 阻止组件渲染



### 阻止组件渲染

在极少数情况下，你可能希望隐藏组件，即使它被其他组件渲染。让 render 方法返回 null 而不是它的渲染结果即可实现。

在下面的例子中，**<WarningBanner />** 根据属性 warn 的值条件渲染。如果 warn 的值是 false，则组件不会渲染：

```jsx
import React from 'react';

// 定义一个警告组件
function WarningBanner(props) {
  const {warn}=props;
  if (!warn) {
    return null;
  }
  return (
    <div className="warning">
      ---------警告!---------
    </div>
  );
}

class Warning extends React.Component {
  constructor(props) {
    super(props);
    this.state = {showWarning: true}
    this.handleToggleClick = this.handleToggleClick.bind(this);
  }

  handleToggleClick() {
    this.setState(prevState => ({
      showWarning: !prevState.showWarning
    }));
  }

  render() {
    const {showWarning}=this.state;
    return (
      <div>
        <div>阻止组件渲染的例子</div>
        <WarningBanner warn={showWarning} />
        <button type='button' onClick={this.handleToggleClick}>
          {showWarning ? '隐藏' : '显示'}
        </button>
      </div>
    );
  }
}

export default Warning;
```

组件的 render 方法返回 null 并不会影响该组件生命周期方法的回调。例如，componentWillUpdate 和 componentDidUpdate 依然可以被调用。



## React 列表 & Keys

* 每个列表元素分配一个 key，不然会出现警告
* 一个元素的 key 最好是这个元素在列表中拥有的一个独一无二的字符串。通常，我们使用来自数据的 id 作为元素的 key
* 元素的 key 只有在它和它的兄弟节点对比时才有意义。如果你提取出一个 ListItem 组件，你应该把 key 保存在数组中的这个 **<ListItem />** 元素上，而不是放在 ListItem 组件中的 **<li>** 元素上。
* 元素的 key 在他的兄弟元素之间应该唯一
* key 会作为给 React 的提示，但不会传递给你的组件。如果您的组件中需要使用和 key 相同的值，请将其作为属性传递



[官方文档](http://www.runoob.com/react/react-lists-and-keys.html)



```jsx
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li key={number.toString()}>
    {number}
  </li>
);
```



##  React 组件 API

在本章节中我们将讨论 React 组件 API。我们将讲解以下7个方法:

- 设置状态：setState
- 替换状态：replaceState
- 设置属性：setProps
- 替换属性：replaceProps
- 强制更新：forceUpdate
- 获取DOM节点：findDOMNode
- 判断组件挂载状态：isMounted 方法在 ES6 中已经废除

[详细内容](http://www.runoob.com/react/react-component-api.html)



## React AJAX

React 组件的数据可以通过 **componentDidMount** 方法中的 Ajax 来获取，当从服务端获取数据时可以将数据存储在 state 中，再用 this.setState 方法重新渲染 UI。

当使用异步加载数据时，在组件卸载前使用 **componentWillUnmount** 来取消未完成的请求。

在AntDesignPro中，有自己特殊的处理模式。



## React 表单与事件



### 一个简单的实例

在实例中我们设置了输入框 input 值 **value = {this.state.data}**。在输入框值发生变化时我们可以更新 state。我们可以使用 **onChange** 事件来监听 input 的变化，并修改 state。

```jsx
import React from 'react';

class HelloMessage extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'Hello Runoob!'};
    this.handleChange = this.handleChange.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }
  
  render() {
    const {value} = this.state;
    return (
      <div>
        <h1>React 表单与事件:</h1>
        <input type="text" value={value} onChange={this.handleChange} />
        <h4>{value}</h4>
      </div>
    );
  }
}

export default HelloMessage;
```



### 在子组件上使用表单

在以下实例中我们将为大家演示如何在子组件上使用表单。 **onChange** 方法将触发 state 的更新并将更新的值传递到子组件的输入框的 **value** 上来重新渲染界面。

你需要在父组件通过创建事件句柄 (**handleChange**) ，并作为 prop (**updateStateProp**) 传递到你的子组件上。

```jsx
import React from 'react';

function Content(props) {
  const {myDataProp,updateStateProp}=props;
  return(
    <div>
      <input type="text" value={myDataProp} onChange={updateStateProp} />
      <h4>{myDataProp}</h4>
    </div>
  );
}

class HelloMessage extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'Hello Runoob!'};
    this.handleChange = this.handleChange.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  render() {
    const {value} = this.state;
    return (
      <div>
        <h1>React 表单与事件:</h1>
        <Content
          myDataProp={value}
          updateStateProp={this.handleChange}
        />
      </div>
    );
  }
}

export default HelloMessage;
```



### 处理多个输入

当需要处理多个 `input` 元素时，我们可以给每个元素添加 `name` 属性，并让处理函数根据 `event.target.name` 的值选择要执行的操作。

```jsx
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputChange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name;

    this.setState({
      [name]: value
    });
  }

  render() {
    return (
      <form>
        <label>
          参与:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          来宾人数:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```

这里使用了 ES6 [计算属性名称](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names)的语法更新给定输入名称对应的 state 值：

```jsx
this.setState({
  [name]: value
});
```

如果你想寻找包含验证、追踪访问字段以及处理表单提交的完整解决方案，使用 [Formik](https://jaredpalmer.com/formik) 是不错的选择。然而，它也是建立在受控组件和管理 state 的基础之上——所以不要忽视学习它们。



### 非受控组件

在大多数情况下，我们推荐使用 受控组件 来处理表单数据。在一个受控组件中，表单数据是由 React 组件来管理的。另一种替代方案是使用非受控组件，这时表单数据将交由 DOM 节点来处理。











## Select 下拉菜单

在 React 中，不使用 selected 属性，而在根 select 标签上用 value 属性来表示选中项。

```jsx
import React from 'react';

class FlavorForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'coconut'};
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    const {value}=this.state;
    alert(value);
    event.preventDefault();
  }

  render() {
    const {value}=this.state;
    return (
      <form onSubmit={this.handleSubmit}>
        选择您最喜欢的网站
        <select value={value} onChange={this.handleChange}>
          <option value="gg">Google</option>
          <option value="rn">Runoob</option>
          <option value="tb">Taobao</option>
          <option value="fb">Facebook</option>
        </select>
        <input type="submit" value="提交" />
      </form>
    );
  }
}

export default FlavorForm;
```



## React Refs

refs 就相当于jquery中的id，能快速定位到组件。

```jsx
class MyComponent extends React.Component {
  handleClick() {
    // 使用原生的 DOM API 获取焦点
    this.refs.myInput.focus();
  }
  render() {
    //  当组件插入到 DOM 后，ref 属性添加一个组件的引用于到 this.refs
    return (
      <div>
        <input type="text" ref="myInput" />
        <input
          type="button"
          value="点我输入框获取焦点"
          onClick={this.handleClick.bind(this)}
        />
      </div>
    );
  }
}
export default MyComponent;
```













