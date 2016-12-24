# 介绍 JSX

考虑下面的变量声明：

```
const element = <h1>Hello, world!</h1>;
```

这是个有趣的标签语法，它既不是一个字符串，也不是 HTML。

它叫 JSX，是对 JavaScript 的语法扩展。我们推荐结合 React 使用它来描述 UI 看起来应该是什么样。JSX 可能让你想起模板语言，但是它具有 JavaScript 的全部能力。

JSX 生成 React “元素”。我们我们将在下一章探索把它们渲染到 DOM。下面，你可以找到让你开始的 JSX 必备的基础知识。

### 在 JSX 中插入表达式

你可以通过包裹在大括号中在 JSX 插入任意的 JavaScript 表达式。例如，`2+2`、`user.name`和`formatName(user)`都是有效的表达式：

```
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

在[CodePen](http://codepen.io/gaearon/pen/PGEjdG?editors=0010)试试。

为了可读性，我们拆分 JSX 为多行。虽然这不是强制的，当这样做时，我们还推荐把它包裹在括号中来避免 js 自动插入分号的陷阱。

### JSX 也是表达式

经过编译后，JSX 表达式变成普通的 JavaScript 对象。

这意味着，你可以在`if`语句和`for`循环中使用 JSX、把它赋值给变量、作为参数接收、从函数返回。

```
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

### 使用 JSX 指定属性

你可以使用引号来指定字符串字面量来作为属性:

```
const element = <div tabIndex="0"></div>;
```

你也可以使用大括号在属性中插入 JavaScript 表达式：

```
const element = <img src={user.avatarUrl}></img>;
```

### 使用 JSX 指定子元素

如果一个标签是空元素。你可以像 XML 一样使用`/>`自闭合标签：

```
const element = <img src={user.avatarUrl} />;
```

JSX 标签可以包含子元素：

```
const element = (
  <div>
    <h1>Hello!</h1>
    <h2>Good to see you here.</h2>
  </div>
);
```

> **警告**
>
> 由于 JSX 相比于 HTML 更接近 JavaScript，ReactDOM 使用`cameCase`（驼峰命名）作为属性命名约定代替 HTML 属性名。
>
> 例如，在 JSX 中`class`变成`className`，`tabindex`变成`tabIndex`。

### JSX 预防注入攻击

在 JSX 中嵌入用户的输入是安全的。

```
const title = response.potentiallyMaliciousInput;
// 这是安全的:
const element = <h1>{title}</h1>;
```

默认，在渲染之前，ReactDOM 会转换任何在 JSX 中插入的值。因此来确保在你的应用中永远不能注入任何没有明确书写的东西。所有的东西在开始渲染前都被转换成字符串。这有助于预防XXS（跨站脚本攻击）。

### JSX 表示对象

Babel 编译 JSX 直到 `React.createElement()`调用。

这两个例子是完全相同的：

```
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

```
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

`React.createElement()`执行一些检查帮助你写出没有bug的代码，但本质上，它创建一个像这样的对象：

```
// 注意：这个结构是简化的
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world'
  }
};
```
这些对象称为“React元素”。你可以把它们想象成你想在屏幕上看到的东西的描述。React读取这些对象，并使用它们构造DOM并保持更新。

我们将在下一章节探索渲染 React 元素到DOM。

> **Tip:**
>
> 我们推荐为你选的编辑器搜索“Babel”语法主题，以便于 ES6 和 JSX 代码正确的高亮显示。

> 下一步
>
> [渲染元素](./渲染元素.md)