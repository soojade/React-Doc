# State 和生命周期

考虑前面章节中时钟的例子。

到目前位置，我们仅学习了一种更新 UI 的方式。

我们调用`ReactDOM.render()`来改变渲染输出：

```
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(
    element,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

在[CodePen](http://codepen.io/gaearon/pen/gwoJZk?editors=0010)上试试。

在这一章节，我们将学习怎样使`Clock`成为真正的可复用和封装的组件。它将设置它自己的计时器，并且每一秒更新它自己。

我们可以从封装时钟的外观开始：

```
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

在[CodePen](http://codepen.io/gaearon/pen/dpdoYR?editors=0010)上试试。

然而，这遗漏了重要的一点：事实上，`Clock`设置一个计时器并每秒更新 UI 应该是`Clock`的实现细节。

理想中，我们想要像下面这样写一次，然后`Clock`更新它自身：

```
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

为了实现它，我们需要给`Clock`组件添加“state”。

State 类似于 props，但它是私有的并且完全由组件控制。

我们之前提到的，组件定义为类时有一些额外的特性。本地 state 正是这样：一个只有类可用的特性。

## 由函数转换到类

你可以通过五步将函数式组件转换成类组件，比如`Clock`：

1. 使用相同的名字创建一个继承自`React.Component`的 ES6 类。
2. 添加一个空的`render()`方法。
3. 把函数体移动到`render()`方法中。
4. 在`render()`方法中使用`this.props`代替`props`。
5. 删除仅剩的空函数声明。

```
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

在[CodePen](http://codepen.io/gaearon/pen/zKRGpo?editors=0010)上试试。

`Clock`现在被定义为类而不是函数。

这让我们可以使用额外的特性比如：本地 state 和生命周期钩子。

## 添加本地 State 到一个类

我们将通过三步将`date`从`props`移到`state`。

1）在`render()`方法中使用`this.state.date`替换`this.props.date`

```
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

2）添加一个构造函数来初始化`this.state`：

```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

注意我们是怎样传递`props`给基类构造函数的：

```
constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
```

组件类应该总是通过`props`调用基类的构造函数。

3）从`<Clock />`元素移除`date`属性：

```
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

我们稍后将定时器代码添加回组件本身。

最终看起来是这个样子：

```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

在[CodePen](http://codepen.io/gaearon/pen/KgQpJd?editors=0010)上试试。

接下来，我们将使`Clock`设置自己的计时器，并每秒更新它自身。

## 添加生命周期方法到一个类

在应用中有很多组件，当它们被销毁时，释放这些组件所占用的资源是很重要的。

我们想要每当`Clock`被首次渲染到 DOM时设置一个计时器。这在 React 中称为“挂载”。

我们也想每当通过`Clock`生成的 DOM 被移除时清除这个计时器。这在 React 中称为“卸载”。

当一个组件在挂载和卸载的时候，我们可以在组件类中声明特殊的方法来运行一些代码：

```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {

  }

  componentWillUnmount() {

  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

这些方法被称为“生命周期钩子”。

`componentDidMount()`钩子在组件输出已经被渲染到 DOM 之后运行。这是设置一个计时器的好地方：

```
componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }
```

注意我们如何在`this`的右边保存计时器的 ID。

虽然`this.props`是由 React 本身设置的，并且`this.state`有一个特殊的意义，如果你需要存储一些不被用来显示输出的东西，你可以自由的手动把字段添加到类。

如果你不在`render()`中使用一些东西，就不应该使用 state。

我们将会在`componentWillUnmount()`生命周期钩子中拆卸计时器：

```
componentWillUnmount() {
    clearInterval(this.timerID);
  }
```

最后，我们将实现`tick()`方法，它将每隔一秒运行一次。

它会使用`this.setState()`来安排更新到组件的本地 state：

```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

在[CodePen](http://codepen.io/gaearon/pen/amqdNA?editors=0010)上试试。

现在时钟每秒钟都会“嘀嗒”一次.

让我们快速回顾发生了什么，以及方法被调用的顺序：

1. 当`<Clock />`被传递到`ReactDOM.render()`时，React 调用`Clock`组件的构造函数。由于`Clock`需要显示当前的时间，我们使用一个包含当前时间的对象初始化`this.state`。我们稍后将更新这个 state。
2. React 接着调用`Clock`组件的`render()`方法。在这里 React 知道了应该在屏幕上显示什么。React 接着更新 DOM 来匹配`Clock`的渲染输出。
3. 当`Clock`输出被插入进 DOM 时，React 调用`componentDidMount()`生命周期钩子。在其内部，`Clock`组件要求浏览器设置一个计时器来一秒钟调用一次`tick()`。
4. 每一秒浏览器调用一次`tick()`方法。在其内部，`Clock`组件通过调用一个使用包含当前时间的对象作为参数的`setState()`方法来安排 UI 的更新。由于`setState()`的调用，React 知道了 state 已经改变，并且再次调用`render()`方法获取应该在屏幕上显示的内容。这一次，`render()`方法中的`this.state.date`是不同的，所以渲染输出会包含更新的时间。React 相应的更新 DOM。
5. 如果`Clock`组件永久的从 DOM 移除，React 调用`componentWillUnmount()`生命周期钩子，因此计时器停止了。

## 正确的使用 State

关于`setState()`有三件事你应该知道。

### 不要直接修改 State

例如，这将不会重新渲染一个组件：

```
// 错误
this.state.comment = 'Hello';
```

相反，使用`setState()`:

```
// 改正后
this.setState({comment: 'Hello'});
```

唯一可以给`this.state`赋值的地方是在构造函数中。

### State 更新可能是异步的

出于性能考虑，React 可能在一次更新中调用多次`setState()`。

由于`this.props`和`this.state`的更新可能是异步的，你不应该依赖它们的值来计算下一个 state。

例如，下面这段代码更新`counter`会失败：

```
// 错误
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

我们使用`setState()`的第二种形式：接收一个函数而不是对象，来修复它。这个函数会接收之前的 state 作为第一个参数，并且在当时应用于更新的 props 作为第二个参数：

```
// 改正后
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));
```

上面我们使用了*箭头函数*，使用普通的函数也是有效的:

```
this.setState(function(prevState, props) {
  return {
    counter: prevState.counter + props.increment
  };
});
```

### State 的更新是合并的

当你调用`setState()`时，React 会合并你提供的对象到当前的 state。

例如，你的 state 可能包含几个独立的变量：

```
constructor(props) {
    super(props);
    this.state = {
      posts: [],
      comments: []
    };
  }
```

然后你可以分开调用`setState()`单独的更新它们。

```
componentDidMount() {
    fetchPosts().then(response => {
      this.setState({
        posts: response.posts
      });
    });

    fetchComments().then(response => {
      this.setState({
        comments: response.comments
      });
    });
  }
```

因为是浅合并，所以`this.setState({comments})`并没有使`this.state.posts`发生变化，但`this.state.comments`完全被替换了。

## 数据流向

无论是父组件还是子组件，都不知道某个组件是有状态还是无状态，并且它们也不应该关心它被定义为一个函数还是一个类。

这就是为什么 state 通常被局部调用或封装。除了拥有和设置它的组件之外的任何组件都不能访问它。

一个组件可以选择将它自身的 state 作为 props 向下传递给它的子组件。

```
<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
```

这也适用于用户自定义组件：

```
<FormattedDate date={this.state.date} />
```

`FormattedDate`组件会接收`date`作为它的 props，并且不会知道它是否来自`Clock`的 state、props，还是手动输入的。

```
function FormattedDate(props) {
  return <h2>It is {props.date.toLocaleTimeString()}.</h2>;
}
```

在[CodePen](http://codepen.io/gaearon/pen/zKRqNB?editors=0010)上试试。

这通常被称为“自上而下”的或“单向”数据流。任何 state 始终隶属于一些特定的组件，并且任何来源于这个 state 的数据或 UI 只能影响到组件树“下面”的组件。

如果你把组件树想象成一个由 props 构成的瀑布，每一个组件的 sate 就像额外的水源，会在任意的节点汇入这个瀑布并且随之向下流。

为了展示所有的组件都是真正的独立的，我们可以创建一个`App`组件，渲染三个`<Clock>`：

```
function App() {
  return (
    <div>
      <Clock />
      <Clock />
      <Clock />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

在[CodePen](http://codepen.io/gaearon/pen/vXdGmd?editors=0010)上试试。

每个`Clock`设置它自己的定时器，并独立更新。

在 React 应用中，不管组件是有状态的还是无状态的，都被认为组件的实现细节可以随时间发生改变的。你可以在有状态的组件内使用无状态的组件，反之亦然。

> 下一步
>
> [事件处理](./事件处理.md)