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
如果你构建的组件有很多子元素，像表单（form），你可能以大量的变量声明结束。
```
// Awkward block of variable declarations
var Form = MyFormComponent;
var FormRow = Form.Row;
var FormLabel = Form.Label;
var FormInput = Form.Input;

var App = (
  <Form>
    <FormRow>
      <FormLabel />
      <FormInput />
    </FormRow>
  </Form>
);
```
为了使它更简单和容易，组件命名空间让你使用一个拥有其他组件作为属性的组件：
```
var Form = MyFormComponent;

var App = (
  <Form>
    <Form.Row>
      <Form.Label />
      <Form.Input />
    </Form.Row>
  </Form>
);
```
为此，你只需要创建你的*子组件*作为主组件的属性：
```
var MyFormComponent = React.createClass({ ... });

MyFormComponent.Row = React.createClass({ ... });
MyFormComponent.Label = React.createClass({ ... });
MyFormComponent.Input = React.createClass({ ... });
```
当编译代码时，JSX会做正确的处理。
```
var App = (
  React.createElement(Form, null,
    React.createElement(Form.Row, null,
      React.createElement(Form.Label, null),
      React.createElement(Form.Input, null)
    )
  )
);
```

>**注意**：

>这个特性只有在v0.11之后的版本可用

## JavaScript 表达式
### 属性表达式
要使用 JavaScript 表达式作为属性值，只需把这个表达式用一对大括号 ({}) 包起来，不要用引号 ("")。
```
// Input (JSX):
var person = <Person name={window.isLoggedIn ? window.name : ''} />;
// Output (JS):
var person = React.createElement(
  Person,
  {name: window.isLoggedIn ? window.name : ''}
);
```

### 布尔属性
省略的属性值，JSX视为 `true`。传递 `false` 属性表达式将被使用。当使用 HTML 表单元素时经常出现，这些属性，像 `disabled`, `checked` 还有 `readOnly`。
```
// These two are equivalent in JSX for disabling a button
<input type="button" disabled />;
<input type="button" disabled={true} />;

// And these two are equivalent in JSX for not disabling a button
<input type="button" />;
<input type="button" disabled={false} />;
```

### 子节点表达式
同样地，JavaScript 表达式可用于描述子结点：
```
// Input (JSX):
var content = <Container>{window.isLoggedIn ? <Nav /> : <Login />}</Container>;
// Output (JS):
var content = React.createElement(
  Container,
  null,
  window.isLoggedIn ? React.createElement(Nav) : React.createElement(Login)
);
```

### 注释
JSX 里添加注释很容易；它们只是 JS 表达式而已。你只需要在一个标签的子节点内(非最外层)小心地用 {} 包围要注释的部分。
```
var content = (
  <Nav>
    {/* child comment, put {} around */}
    <Person
      /* multi
         line
         comment */
      name={window.isLoggedIn ? window.name : ''} // end of line comment
    />
  </Nav>
);
```

>**注意:**

>JSX 类似于 HTML，但不完全一样。参考 [JSX 陷阱](JSX的陷阱.md) 了解主要不同。