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

## 为一个类添加本地 State

