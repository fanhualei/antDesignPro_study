# 例子：连连看

[TOC]





## React 哲学-开发思路

[原文](https://zh-hans.reactjs.org/docs/thinking-in-react.html)

- 从设计稿开始
  - 假设我们已经有了一个返回 JSON 的 API
- 第一步：将设计好的 UI 划分为组件层级
- 第二步：用 React 创建一个静态版本
  - 补充说明: 有关 props 和 state
- 第三步：确定 UI state 的最小（且完整）表示
- 第四步：确定 state 放置的位置
- 第五步：添加反向数据流



## 第一步：将设计好的 UI 划分为组件层级



![alt](imgs\game.png)

* Game蓝色框最外围
  * Board 红色框9宫格（Square 小方框，小方格。）
  * Message 紫色框最外围



## 第二步：用 React 创建一个静态版本

当你的应用比较简单时，使用自上而下的方式更方便；对于较为大型的项目来说，自下而上地构建，并同时为低层组件编写测试是更加简单的方式。

为了方便静态页面显示，可以先模拟一个Json数据。

```json
{
    history:[
        {squares:[null,null,null,null,null,null,null,null,null,],xIsNext:true},
        {squares:[X,null,null,null,null,null,null,null,null,],xIsNext:true},
        {squares:[X,O,null,null,null,null,null,null,null,],xIsNext:true},
    ]    
}
```



> 做一个静态的程序

```jsx
import React, { useState} from 'react';
import styles from './Game.less';

const data={
  history:[
    {squares:[null,null,null,null,null,null,null,null,null,],xIsNext:true},
    {squares:['X',null,null,null,null,null,null,null,null,],xIsNext:true},
    {squares:['X','O',null,null,null,null,null,null,null,],xIsNext:true},
  ]
};

// 棋盘组件
function Board(props) {
  const {squares}=props;
  const renderSquare = (i) => {
    return(
      <button type='button' className={styles.square}>
        {squares[i]}
      </button>
    )
  }
  return(
    <div>
      <div className={styles['board-row']}>
        {renderSquare(0)}
        {renderSquare(1)}
        {renderSquare(2)}
      </div>
      <div className={styles['board-row']}>
        {renderSquare(3)}
        {renderSquare(4)}
        {renderSquare(5)}
      </div>
      <div className={styles['board-row']}>
        {renderSquare(6)}
        {renderSquare(7)}
        {renderSquare(8)}
      </div>
    </div>
  );
}

const strWinder="获胜者：";
const strNextTs="下个玩家：";
// 每部信息组件
function Info(props) {
  const {history}=props;
  const current = history[history.length - 1];
  const winner = false;
  let status;
  if (winner) {
    status = strWinder + winner;
  } else {
    status = strNextTs + (current.xIsNext ? 'X' : 'O');
  }
  const strBack= '退回到 #';
  const moves = history.map((value, index) => {
    const desc = index ?
      strBack + index :
      '从新开始游戏';
    return (
      <li key={value}>
        <button type='button'>{desc}</button>
      </li>
    );
  });
  return(
    <div className={styles['game-info']}>
      <div>{status}</div>
      <ol>{moves}</ol>
    </div>
  );
}

function Game () {
  const {history}=data;
  const current=history[history.length - 1].squares;
  return(
    <div className={styles.game}>
      <div className={styles['game-board']}>
        <Board squares={current} />
      </div>
      <Info history={history} />
    </div>
  );

}

export default Game;
```



## 第三步：确定 UI state 的最小（且完整）表示

我们的示例应用拥有如下数据：

- 当前棋盘上选中的数据（历史数据）
- 下一个用户的提示

通过问自己以下三个问题，你可以逐个检查相应数据是否属于 state：

1. 该数据是否是由父组件通过 props 传递而来的？如果是，那它应该不是 state。
2. 该数据是否随时间的推移而保持不变？如果是，那它应该也不是 state。
3. 你能否根据其他 state 或 props 计算出该数据的值？如果是，那它也不是 state。



综上所述，属于 state 的有：

- 每一步的记录数
- 下一个要下棋的用户



## 第四步：确定 state 放置的位置

对于应用中的每一个 state：

- 找到根据这个 state 进行渲染的所有组件。
- 找到他们的共同所有者（common owner）组件（在组件层级上高于所有需要该 state 的组件）。
- 该共同所有者组件或者比它层级更高的组件应该拥有该 state。
- 如果你找不到一个合适的位置来存放该 state，就可以直接创建一个新的组件来存放该 state，并将这一新组件置于高于共同所有者组件层级的位置。



发现Board 与Message的数据都是propes传进来的，所以state应该放在Game中



## 第五步：添加反向数据流



### 首先添加一个判断是否胜出的函数



> 将可能胜出的情况列出来，然后对比是否可以胜出。

```jsx
function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length;  i+=1) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```



### 在Game组件中添加代码

####  添加state

```jsx
  const [history, setHistory] = useState(
    [{ squares: Array(9).fill(null),xIsNext:true
    }]);
```



#### 添加处理函数 handleClick

当某一个小格子被点击后，要判断是否胜出，如果胜出就提示结束。如果没有胜出，就记录当前棋盘情况，并将数据保存到记录中。

```jsx
  const handleClick=(i)=> {
    const historyTemp = history;
    const current = historyTemp[history.length - 1];
    // 数组类型赋值，必须要新创建一个数组
    const squaresTemp = current.squares.slice();
    const xIsNextTemp=current.xIsNext;
    if (calculateWinner(squaresTemp) || squaresTemp[i]) {
      return;
    }
    squaresTemp[i] = xIsNextTemp ? 'X':'O';
    setHistory(historyTemp.concat([{
      squares: squaresTemp,
      xIsNext:!xIsNextTemp
    }]));
  }
```



#### 将handleClick传递给squares组件

> 必须传递一个箭头函数下去。

```jsx
<Board squares={current.squares} handleClick={(i) => handleClick(i)}  />
```



> 下面是错误的写法

```jsx
// 下面是错误的写法
<Board squares={current.squares} handleClick={handleClick(i)}  />
```



### 在Board中添加代码



```jsx
//得到父亲节点的函数
const {squares,handleClick}=props;
//调用父亲节点函数
<button type='button' className={styles.square} onClick={()=>handleClick(i)}>
```



### 总体代码

```jsx
import React, { useState} from 'react';
import styles from './Game.less';

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length;  i+=1) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}

// 棋盘组件
function Board(props) {
  const {squares,handleClick}=props;
  const renderSquare = (i) => {
    return(
      <button type='button' className={styles.square} onClick={()=>handleClick(i)}>
        {squares[i]}
      </button>
    )
  }
  return(
    <div>
      <div className={styles['board-row']}>
        {renderSquare(0)}
        {renderSquare(1)}
        {renderSquare(2)}
      </div>
      <div className={styles['board-row']}>
        {renderSquare(3)}
        {renderSquare(4)}
        {renderSquare(5)}
      </div>
      <div className={styles['board-row']}>
        {renderSquare(6)}
        {renderSquare(7)}
        {renderSquare(8)}
      </div>
    </div>
  );
}

const strWinder="获胜者：";
const strNextTs="下个玩家：";
// 每部信息组件
function Info(props) {
  const {history}=props;
  const current = history[history.length - 1];
  const winner = calculateWinner(current.squares);
  let status;
  if (winner) {
    status = strWinder + winner;
  } else {
    status = strNextTs + (current.xIsNext ? 'X' : 'O');
  }
  const strBack= '退回到 #';
  const moves = history.map((value, index) => {
    const desc = index ?
      strBack + index :
      '从新开始游戏';
    return (
      <li key={value}>
        <button type='button'>{desc}</button>
      </li>
    );
  });
  return(
    <div className={styles['game-info']}>
      <div>{status}</div>
      <ol>{moves}</ol>
    </div>
  );
}

function Game () {
  const [history, setHistory] = useState(
    [{ squares: Array(9).fill(null),xIsNext:true
    }]);
  const handleClick=(i)=> {
    const historyTemp = history;
    const current = historyTemp[history.length - 1];
    // 数组类型赋值，必须要新创建一个数组
    const squaresTemp = current.squares.slice();
    const xIsNextTemp=current.xIsNext;
    if (calculateWinner(squaresTemp) || squaresTemp[i]) {
      return;
    }
    squaresTemp[i] = xIsNextTemp ? 'X':'O';
    setHistory(historyTemp.concat([{
      squares: squaresTemp,
      xIsNext:!xIsNextTemp
    }]));
  }
  const current=history[history.length - 1];
  return(
    <div className={styles.game}>
      <div className={styles['game-board']}>
        <Board squares={current.squares} handleClick={(i) => handleClick(i)}  />
      </div>
      <Info history={history} />
    </div>
  );

}

export default Game;
```



## 第六步：添加悔棋功能



### 在Game中添加代码

#### 添加跳转函数

```jsx
  const jumpTo=(stepIndex)=>{
    let stepIndexTemp=stepIndex+1;
    if (stepIndex === history.length) {
      stepIndexTemp = stepIndex;
    } else {
      stepIndexTemp = stepIndex+ 1;
    }
    const historyTemp = history.slice(0,stepIndexTemp);
    setHistory(historyTemp);
  }
```

#### 将函数传递到Info组件

```js
<Info history={history} jumpTo={(i) =>jumpTo(i)} />
```



### 在Info组件中添加代码

```js
// 得到传递过来的代码
const {history,jumpTo}=props;

// 在button事件上绑定clicked
<button type='button' onClick={()=>jumpTo(index)}>{desc}</button>
```



## 最完整的代码

```js
import React, { useState} from 'react';
import styles from './Game.less';

function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length;  i+=1) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}

// 棋盘组件
function Board(props) {
  const {squares,handleClick}=props;
  const renderSquare = (i) => {
    return(
      <button type='button' className={styles.square} onClick={()=>handleClick(i)}>
        {squares[i]}
      </button>
    )
  }
  return(
    <div>
      <div className={styles['board-row']}>
        {renderSquare(0)}
        {renderSquare(1)}
        {renderSquare(2)}
      </div>
      <div className={styles['board-row']}>
        {renderSquare(3)}
        {renderSquare(4)}
        {renderSquare(5)}
      </div>
      <div className={styles['board-row']}>
        {renderSquare(6)}
        {renderSquare(7)}
        {renderSquare(8)}
      </div>
    </div>
  );
}

const strWinder="获胜者：";
const strNextTs="下个玩家：";
// 每部信息组件
function Info(props) {
  const {history,jumpTo}=props;
  const current = history[history.length - 1];
  const winner = calculateWinner(current.squares);
  let status;
  if (winner) {
    status = strWinder + winner;
  } else {
    status = strNextTs + (current.xIsNext ? 'X' : 'O');
  }
  const strBack= '退回到 #';
  const moves = history.map((value, index) => {
    const desc = index ?
      strBack + index :
      '从新开始游戏';
    return (
      <li key={value}>
        <button type='button' onClick={()=>jumpTo(index)}>{desc}</button>
      </li>
    );
  });
  return(
    <div className={styles['game-info']}>
      <div>{status}</div>
      <ol>{moves}</ol>
    </div>
  );
}

function Game () {
  const [history, setHistory] = useState(
    [{ squares: Array(9).fill(null),xIsNext:true
    }]);
  const handleClick=(i)=> {
    const historyTemp = history;
    const current = historyTemp[history.length - 1];
    // 数组类型赋值，必须要新创建一个数组
    const squaresTemp = current.squares.slice();
    const xIsNextTemp=current.xIsNext;
    if (calculateWinner(squaresTemp) || squaresTemp[i]) {
      return;
    }
    squaresTemp[i] = xIsNextTemp ? 'X':'O';
    setHistory(historyTemp.concat([{
      squares: squaresTemp,
      xIsNext:!xIsNextTemp
    }]));
  }

  const jumpTo=(stepIndex)=>{
    let stepIndexTemp=stepIndex+1;
    if (stepIndex === history.length) {
      stepIndexTemp = stepIndex;
    } else {
      stepIndexTemp = stepIndex+ 1;
    }
    const historyTemp = history.slice(0,stepIndexTemp);
    setHistory(historyTemp);
  }

  const current=history[history.length - 1];
  return(
    <div className={styles.game}>
      <div className={styles['game-board']}>
        <Board squares={current.squares} handleClick={(i) => handleClick(i)}  />
      </div>
      <Info history={history} jumpTo={(i) =>jumpTo(i)} />
    </div>
  );

}

export default Game;


```



## 通过DVA来实现

DVA实现了解耦操作，原先的函数与state都写在了UI里面现在需要拆除到一个Model中





### 第一步：将设计好的 UI 划分为组件层级(同上）





### 第二步：用 React 创建一个静态版本(同上）



### 第三步：确定 UI state 的最小（且完整）表示(同上）





### 第四步：确定 state 放置的位置



####  首先建立一个Model

在Model中先添加state，先不用添加方法。

```js
export default {
  // 命名空间名字，必填
  namespace: 'game',

  state: {
    history: [{ squares: Array(9).fill(null), xIsNext: true }],
  },
}  
```



#### 修改顶层UI

```js
// 得到game.js中的state的history
export default connect(({ game }) => ({
  history: game.history,
}))(Game);

// 将从game中得到的history与dispath传递给Game空间
function Game({ history, dispatch }) {
  const current = history[history.length - 1];
  return (
    <div className={styles.game}>
      <div className={styles['game-board']}>
        <Board squares={current.squares} />
      </div>
      <Info history={history}  />
    </div>
  );
}

```



## 第五步：添加反向数据流



### 在model中添加两个函数

>  reducers 中 handleClick与jumpTo函数

* 参数state与action ,其中action是函数中的参数。
* 这两个函数，返回一个新的state

```jsx
export default {
  // 命名空间名字，必填
  namespace: 'game',

  state: {
    history: [{ squares: Array(9).fill(null), xIsNext: true }],
  },

  reducers: {
    handleClick(state, action) {
      const { clickIndex } = action.payload;
      const { history } = state;
      const current = history[history.length - 1];
      // 数组类型赋值，必须要新创建一个数组
      const { squares, xIsNext } = current;
      if (calculateWinner(squares) || squares[clickIndex]) {
        return state;
      }
      const newSquares = squares.slice();
      newSquares[clickIndex] = xIsNext ? 'X' : 'O';
      return {
        history: history.concat([
          {
            squares: newSquares,
            xIsNext: !xIsNext,
          },
        ]),
      };
    },

    jumpTo(state, action) {
      const { stepIndex } = action.payload;
      const { history } = state;
      let stepIndexTemp = stepIndex + 1;
      if (stepIndex === history.length) {
        stepIndexTemp = stepIndex;
      } else {
        stepIndexTemp = stepIndex + 1;
      }
      const historyTemp = history.slice(0, stepIndexTemp);
      return { history: historyTemp };
    },
  },
};
```

### 在UI中添加相应的函数

```js
function Game({ history, dispatch }) {
  const handleClick = i => {
    dispatch({
      type: 'game/handleClick',
      payload: {
        clickIndex: i,
      },
    });
  };

  const jumpTo = i => {
    dispatch({
      type: 'game/jumpTo',
      payload: {
        stepIndex: i,
      },
    });
  };

  const current = history[history.length - 1];
  return (
    <div className={styles.game}>
      <div className={styles['game-board']}>
        <Board squares={current.squares} handleClick={i => handleClick(i)} />
      </div>
      <Info history={history} jumpTo={i => jumpTo(i)} />
    </div>
  );
}
```



### 添加一个异步函数

* payload  是外部传入的参数
* call 调用外部函数
* pub用来调用reducers函数

```js
  effects: {
    *jumpToEx({ payload }, { call, put }) {
      const response = yield call(fakeChartData);
      console.log(response)
      const {stepIndex}=payload;
      yield put({
        type: 'jumpTo',
        payload: {'stepIndex': stepIndex,},
      });
    },
  },
```

> 在UI中调用异步函数，与调用同步函数是一样的。



### 如何在UI中添加初始化函数

```js
  useEffect(() => {
    console.log("start=======================");
    document.title = `You clicked ${history.length} times`;
    return()=>{
      console.log("end=======================");
    }
  },[]);
```

[官方使用说明](https://zh-hans.reactjs.org/docs/hooks-effect.html)







