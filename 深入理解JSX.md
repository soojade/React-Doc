# 深入理解 JSX
JSX 是一个看起来很像 XML 的 JavaScript 语法扩展。React 可以用来做简单的 JSX 句法转换。

## 为什么要使用 JSX？
你不需要为了 React 使用 JSX，可以直接使用纯粹的 JS。但我们更建议使用 JSX , 因为它能定义简洁且我们熟知的包含属性的树状结构语法。

对于非专职开发者（比如设计师）同样比较熟悉。

XML 有固定的标签开启和闭合。这能让复杂的树更易于阅读，优于方法调用和对象字面量的形式。

它没有修改 JavaScript 语义。

## HTML标签 与 React组件 对比
React 可以渲染 HTML 标签 (strings) 或 React 组件 (classes)。

要渲染 HTML 标签，只需在 JSX 里使用小写字母开头的标签名。
```
var myDivElement = <div className="foo" />;
ReactDOM.render(myDivElement, document.getElementById('example'));
```
要渲染 React 组件，只需创建一个大写字母开头的本地变量。
```
var MyComponent = React.createClass({/*...*/});
var myElement = <MyComponent someProperty={true} />;
ReactDOM.render(myElement, document.getElementById('example'));
```
React 的 JSX 里约定分别使用首字母大、小写来区分本地组件的类和 HTML 标签。

>**注意:**

>由于 JSX 就是 JavaScript，一些标识符像 class 和 for 不建议作为 XML 属性名。作为替代，React DOM 使用 className 和 htmlFor 来做对应的属性。

## 转换
JSX 把类 XML 的语法转成纯粹 JavaScript，XML 元素、属性和子节点被转换成 React.createElement 的参数。
```
var Nav;
// Input (JSX):
var app = <Nav color="blue" />;
// Output (JS):
var app = React.createElement(Nav, {color:"blue"});
```
注意，要想使用 `<Nav />`，`Nav` 变量一定要在作用区间内。

JSX 也支持使用 XML 语法定义子结点：
```
var Nav, Profile;
// Input (JSX):
var app = <Nav color="blue"><Profile>click</Profile></Nav>;
// Output (JS):
var app = React.createElement(
  Nav,
  {color:"blue"},
  React.createElement(Profile, null, "click")
);
```
当displayName 值为undefined，JSX会通过变量的赋值来推断class的 displayName：
```
// Input (JSX):
var Nav = React.createClass({ });
// Output (JS):
var Nav = React.createClass({displayName: "Nav", });
```
使用[Babel REPL](https://babeljs.io/repl/)来试用 JSX 并理解它是如何转换到原生 JavaScript，还有 [HTML to JSX converter](https://facebook.github.io/react/html-jsx.html) 把现有 HTML 转成 JSX。

如果你要使用 JSX，这篇 [新手入门](快速开始.md) 教程来教你如何搭建环境。

> **注意:**

>JSX 表达式总是会当作 ReactElement 执行。具体的实际细节可能不同。一种优化 的模式是把 ReactElement 当作一个行内的对象字面量形式来绕过 React.createElement 里的校验代码。

## 组件命名空间
