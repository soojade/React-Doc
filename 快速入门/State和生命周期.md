# State 和生命周期

考虑前面章节中的时钟的例子。

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

理想中，我们想如下这样写一次，然后`Clock`更新它自身：

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

我们也想每当通过`Clock`生成的 DOM 被移除时清除这个计时器。这在 React 总称为“卸载”。

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

