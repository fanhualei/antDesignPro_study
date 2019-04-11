# React 基数语法笔记

下面文档是在[菜鸟React基本语法基础上](http://www.runoob.com/react/react-tutorial.html)+Ant Design Pro 框架基础上的笔记



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



