# Hello World

最容易开始使用 React 方式是用[CodePen](http://codepen.io/gaearon/pen/ZpvBNJ?editors=0010)。你不需要安装任何东西；你只需要在新页面打开它并依照我们当时经历的例子。如果宁愿使用本地的开发环境，查看*安装*章节。

最小的 React 例子看起来是这样的：

```
ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
);
```

它在页面渲染了一个”Hello World“标题。

下面的几个部分会逐步的介绍你使用 React。我们将考察React 应用的构建单元：元素和组件。一旦你掌握了它们，你就可以从小型可复用的部件创建复杂的应用。

## 关于 JavaScript 的注意事项

React 是一个 JavaScript 库，所以假设你已经对 JavaScript 语言有了基本了解。如果你感到不确信，我们推荐[重新介绍 JavaScript（JS 教程）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/A_re-introduction_to_JavaScript)，这样你就可以更容易地跟随下去。

我们在例子中也使用一些 ES6 语法。我们尽量少用，因为它仍然相对较新，但我们鼓励你熟悉*箭头函数*、*类*、*模板字符串*、`let`和`const`声明。你可以使用[Babel REPL](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/A_re-introduction_to_JavaScript)来查看ES6代码编译成什么样。

> 下一步
>
> [介绍 JSX](./介绍JSX.md)