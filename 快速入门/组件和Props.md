# 组件和 Props

组件让你将 UI 拆分成独立的，可复用的部件，并且把每个部件都隔离出来单独考虑。

从概念上讲，组件就像 JavaScript 函数。它们接收任意的输入（称为“props”）并且返回 React 元素描述什么应该显示在屏幕上。

## 函数式和类组件

最简单的定义组件的方式是写一个 JavaScript 函数：

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

这个函数是一个有效的　React 组件，因为它接收一个带数据的“props”对象参数并且返回一个 React 元素。我们称这些组件为“函数式”，因为它们确实是 JavaScript 函数。

你也可以使用 [ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) 来定义一个组件：

```
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

上面的两个组件在 React 角度看是等价的。

类有一些附加的特性，我们将在接下来的部分讨论。在那之前，为了简洁我们使用函数式组件。

## 渲染组件

之前，我们仅遇到表示 DOM 标签的 React 元素：

```
const element = <div />;
```

然而，元素也可以表示用户自定义组件：

```
const element = <Welcome name="Sara" />;
```

当 React 看到一个元素表示一个用户自定义组件，它会把 JSX 属性作为一个单独的对象传递给组件。我们称这个对象为“props”。

例如，下面这段代码在页面上渲染“Hello，Sara”：

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

在[CodePen](http://codepen.io/gaearon/pen/YGYmEG?editors=0010)上试试。

让我们回顾在这个例子中发生了什么：

1. 我们调用`ReactDOM.render()`方法，传递了`<Welcome name="Sara" />`。
2. React 通过将`{name:'Sara'}`作为 props 调用`Welcome`组件。
3. 我们的`Welcome`组件返回一个`<h1>Hello, Sara</h1>`元素作为结果。
4. React DOM 高效的更新 DOM 来匹配 `<h1>Hello, Sara</h1`。

> **警告：**
>
> 组件名一定要以大写字母开始。
>
> 例如，`<div />`表示一个 DOM 标签，但`<Welcome />`表示一个组件并且要求`Welcome`在作用域内。

## 组合组件

组件可以在它们的输出中引用其它的组件。这让我们为任何级别的细节使用相同的抽象化组件。一个按钮、一个表单、一个对话框、一个页面：在 React 应用中，所有这些通常表示为组件。

例如，我们可以创建一个`App`组件渲染`Welcome`多次：

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

在[CodePen](http://codepen.io/gaearon/pen/KgQKPr?editors=0010)上试试。

通常，新的 React 应用在顶层有一个单一的`App`组件。然而，如果你整合 React 到一个现有的应用中，你可能使用一个小的组件像`Button`自下而上的开始并且按你的方式逐步的到达整个视图结构的顶层。

> **警告：**
>
> 组件必须返回一个单一的根元素。这就是为什么我们添加一个`<div>`包含所有的`<Welcome />`元素。

## 提取组件

不要害怕把组件拆分成更小的组件。

比如，考虑下面这个`Component`组件：

```
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

在[CodePen](http://codepen.io/gaearon/pen/VKQwEo?editors=0010)上试试。

它接收`author`(对象)、`text`(字符串)、`date`(日期)作为 props，描述了社交媒体网站的一条评论。

由于所有的东西都嵌套在一起，这个组件很难被改变，并且各个部分也很难被复用。让我们从中抽取几个组件出来：

首先，我们提取`Avatar`：

```
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />
  );
}
```

这个`Avatar`组件并不需要知道它将被渲染到`Comment`组件中。这就是为什么我们给它起一个更通用的名字：`user`而不是`author`。

我们推荐命名 props 从组件自身的角度出发，而不是它将被使用到的上下文。

现在我们可以将`Comment`组件简化一点点：

```
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <Avatar user={props.author} />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

接下来，我们来提取`UserInfo`组件，它渲染一个`Avatar`组件紧挨着用户名。

```
function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}
```

这次让我们进一步简化`Comment`组件:

```
function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

在[CodePen](http://codepen.io/gaearon/pen/rrJNJY?editors=0010)上试试。

提取组件刚开始可能看起来是项乏味的工作，但是在大型的应用中，有众多可复用的组件是值得的。一个好的经验法则就是：如果你的 UI 的一部分被应用了很多次(`button`、`Panel`、`Avatar`)，或者它本身足够的复杂(`App`、`FeedStory`、`Comment`)，提取可复用的组件是一个很好的选择。

## Props 是只读的

无论你把组件声明为一个函数还是一个类，它绝不能修改自己的 props。考虑下面这个`sum`函数：

```
function sum(a, b) {
  return a + b;
}
```

这样的函数被称为[“纯函数”](https://en.wikipedia.org/wiki/Pure_function)，因为它们不试图改变它们的输入，并且对相同的输入一直返回同样的结果。

相反，下面这个函数是“非纯函数”，因为它改变了它自身的输入。

```
function withdraw(account, amount) {
  account.total -= amount;
}
```

React 非常灵活，但它有一个严格的规则：

**所有的 React 组件对于它们的 props 必须像纯函数一样。**

当然，应用的 UI 是动态的，会随着时间发生变化。在下一章节，我们将介绍新的概念——“state”。State 允许 React 组件在不违反这条规则的情况下，随着时间改变它们的输出，以响应用户的操作、网络的应答以及任何其他的事情。

> 下一步
>
> [状态和生命周期](./状态和生命周期.md)