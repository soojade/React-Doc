# 深入理解 React
在我看来， React 是较早使用 JavaScript 构建大型、快速的 Web 应用程序的技术方案。它已经被我们广泛应用于 Facebook 和 Instagram。

React 众多优秀特征中的其中一部分就是，教会你去重新思考如何构建应用程序。
本文中，我将跟你一起使用 React 构建一个具备搜索功能的产品列表。

## 从原型（ mock ）开始
假设我们已经拥有了一个 JSON API 和设计师设计的原型。我们的设计师显然不够好，因为原型看起来如下：

![mock](https://facebook.github.io/react/img/blog/thinking-in-react-mock.png)

我们的JSON接口返回数据如下：
```
[
  {category: "Sporting Goods", price: "$49.99", stocked: true, name: "Football"},
  {category: "Sporting Goods", price: "$9.99", stocked: true, name: "Baseball"},
  {category: "Sporting Goods", price: "$29.99", stocked: false, name: "Basketball"},
  {category: "Electronics", price: "$99.99", stocked: true, name: "iPod Touch"},
  {category: "Electronics", price: "$399.99", stocked: false, name: "iPhone 5"},
  {category: "Electronics", price: "$199.99", stocked: true, name: "Nexus 7"}
];
```

## 第一步：拆分 UI 组件层级
你要做的第一件事是，为所有组件（及子组件）命名并画上线框图。假如你和设计师一起工作，也许他们已经完成了这项工作，所以赶紧去跟他们沟通！他们的 Photoshop 图层名也许最终可以直接用于你的 React 组件名。

但是你如何知道哪些才能成为组件？想象一下，当你创建一些函数或对象时，用到一些类似的技术。其中一项技术就是单一功能原则，指的是，理想状态下一个组件应该只做一件事，假如它功能逐渐变大就需要被拆分成更小的子组件。

由于你经常需要将一个JSON数据模型展示给用户，因此你需要检查这个模型结构是否正确以便你的 UI （在这里指组件结构）是否能够正确的映射到这个模型上。这是因为用户界面和数据模型在 信息构造 方面都要一致，这意味着将你可以省下很多将 UI 分割成组件的麻烦事。你需要做的仅仅只是将数据模型分隔成一小块一小块的组件，以便它们都能够表示成组件。

![](https://facebook.github.io/react/img/blog/thinking-in-react-components.png)

你会看到，在我们简单的app中有五个组件。下面我已经用斜体标示出每个组件对应的数据。
1. **FilterableProductTable （橘色）：** 包含整个例子的容器
2. **SearchBar （蓝色）：** 接受所有 用户输入（ user input ）
3. **ProductTable （绿色）：** 根据 用户输入（ user input ） 过滤和展示 数据集合（ data collection ）
4. **ProductCategoryRow （青色）：** 为每个 分类（ category ） 展示一列表头
5. **ProductRow （红色）：** 为每个 产品（ product ） 展示一列

如果你仔细观察 `ProductTable` ，你会发现表头（包含“ Name ”和“ Price ”标签）并不是单独的组件。这只是一种个人偏好，也有一定的争论。在这个例子当中，我把表头当做 `ProductTable` 的一部分，因为它是渲染“数据集合”的一份子，这也是` ProductTable `的职责。但是，当这个表头变得复杂起来的时候（例如，添加排序功能），就应该单独地写一个 ProductTableHeader 组件。

既然我们在原型当中定义了这个组件，让我们把这些元素组成一棵树形结构。这很简单。被包含在其它组件中的组件在属性机构中应该是子级

* FilterableProductTable
  * SearchBar
  * ProductTable
    * ProductCategoryRow
    * ProductRow

第二步： 利用 React ，创建一个静态版本
<iframe width="100%" height="600" src="https://jsfiddle.net/reactjs/yun1vgqb/embedded/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

既然已经拥有了组件树，是时候开始实现应用了。最简单的方式就是创建一个应用，这个应用将数据模型渲染到 UI 上，但是没有交互功能。拆分这两个过程是最简单的，因为构建一个静态的版本仅需要大量的输入，而不需要思考；但是添加交互功能却需要大量的思考和少量的输入。我们将会知道这是为什么。

为了创建一个渲染数据模型的应用的静态版本，你将会构造一些组件，这些组件重用其它组件，并且通过 props 传递数据。 props 是一种从父级向子级传递数据的方式。如果你对 state 概念熟悉，那么**不要使用 state **来构建这个静态版本。 state 仅用于实现交互功能，也就是说，数据随着时间变化。因为这是一个静态的应用版本，所以你并不需要 state 。

你可以从上至下或者从下至上来构建应用。也就是说，你可以从属性结构的顶部开始构建这些组件（例如，从 FilterableProductTable 开始），或者从底部开始（ ProductRow ）。在简单的应用中，通常情况下从上至下的方式更加简单；在大型的项目中，从下至上的方式更加简单，这样也可以在构建的同时写测试代码。

在这步结束的时候，将会有一个可重用的组件库来渲染数据模型。这些组件将会仅有 `render()` 方法，因为这是应用的一个静态版本。位于树形结构顶部的组件（ `FilterableProductTable` ）将会使用数据模型作为 prop 。如果你改变底层数据模型，然后再次调用 `ReactDOM.render()` ， UI 将会更新。查看 UI 如何被更新和什么地方改变都是很容易的，因为 React 的单向数据流（也被称作“单向绑定”）保持了一切东西模块化，很容易查错，并且速度很快，没有什么复杂的。

如果你在这步中需要帮助，请查看 [React 文档](https://facebook.github.io/react/docs/)。

### 穿插一小段内容： props 与 state 比较
在 React 中有两种类型的数据“模型”： props 和 state 。理解两者的区别是很重要的；如果你不太确定两者有什么区别，请大致浏览一下官方的 [React 文档](https://facebook.github.io/react/docs/interactivity-and-dynamic-uis.html)。

## 第三步：识别出最小的（但是完整的）代表 UI 的 state
为了使 UI 可交互，需要能够触发底层数据模型的变化。 React 通过 state 使这变得简单。

为了正确构建应用，首先需要考虑应用需要的最小的可变 state 数据模型集合。此处关键点在于精简：不要存储重复的数据。构造出绝对最小的满足应用需要的最小 state 是有必要的，并且计算出其它强烈需要的东西。例如，如果构建一个 TODO 列表，仅保存一个 TODO 列表项的数组，而不要保存另外一个指代数组长度的 state 变量。当想要渲染 TODO 列表项总数的时候，简单地取出 TODO 列表项数组的长度就可以了。

思考示例应用中的所有数据片段，我们有：
* 最初的 products 列表
* 用户输入的搜索文本
* 复选框的值
* 过滤后的 products 列表

让我们分析每一项，指出哪一个是 state 。简单地对每一项数据提出三个问题：
1. 是否是从父级通过 props 传入的？如果是，可能不是 state 。
2. 是否会随着时间改变？如果不是，可能不是 state 。
3. 能根据组件中其它 state 数据或者 props 计算出来吗？如果是，就不是 state 。

初始的 products 列表通过 props 传入，所以不是 state 。搜索文本和复选框看起来像是 state ，因为它们随着时间改变，也不能根据其它数据计算出来。最后，过滤的 products 列表不是 state ，因为可以通过搜索文本和复选框的值从初始的 products 列表计算出来。

所以最终， state 是：
* 用户输入的搜索文本
* 复选框的值

## 第四步：确认 state 的生命周期
<iframe width="100%" height="600" src="https://jsfiddle.net/reactjs/zafjbw1e/embedded/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

OK，我们辨别出了应用的 state 数据模型的最小集合。接下来，需要指出哪个组件会改变或者说*拥有*这个 state 数据模型。

记住： React 中数据是沿着组件树从上到下单向流动的。可能不会立刻明白哪个组件应该拥有哪些 state 数据模型。这对新手通常是最难理解和最具挑战的，因此跟随以下步骤来弄清楚这点：

对于应用中的每一个 state 数据：
* 找出每一个基于那个 state 渲染界面的组件。
* 找出共同的祖先组件（某个单个的组件，在组件树中位于需要这个 state 的所有组件的上面）。
* 要么是共同的祖先组件，要么是另外一个在组件树中位于更高层级的组件应该拥有这个 state 。
* 如果找不出拥有这个 state 数据模型的合适的组件，创建一个新的组件来维护这个 state ，然后添加到组件树中，层级位于所有共同拥有者组件的上面。

让我们在应用中应用这个策略：
* `ProductTable` 需要基于 state 过滤产品列表，`SearchBar` 需要显示搜索文本和复选框状态。
* 共同拥有者组件是 `FilterableProductTable` 。
* 理论上，过滤文本和复选框值位于 `FilterableProductTable` 中是合适的。

太酷了，我们决定了 state 数据模型位于 `FilterableProductTable` 之中。首先，给 `FilterableProductTable` 添加 `getInitialState()` 方法，该方法返回 `{filterText: '', inStockOnly: false}` 来反映应用的初始化状态。然后传递 `filterText` 和 `inStockOnly` 给 `ProductTable` 和 `SearchBar` 作为 prop 。最后，使用这些 props 来过滤 `ProductTable` 中的行，设置在 `SearchBar` 中表单字段的值。

你可以开始观察应用将会如何运行：设置 `filterText` 为 `"ball"` ，然后刷新应用。将会看到数据表格被正确更新了。

## 第五步：添加反向数据流
<iframe width="100%" height="600" src="https://jsfiddle.net/reactjs/n47gckhr/embedded/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

到目前为止，已经构建了渲染正确的基于 props 和 state 的沿着组件树从上至下单向数据流动的应用。现在，是时候支持另外一种数据流动方式了：组件树中层级很深的表单组件需要更新 `FilterableProductTable` 中的 state 。

React 让这种数据流动非常明确，从而很容易理解应用是如何工作的，但是相对于传统的双向数据绑定，确实需要输入更多的东西。 React 提供了一个叫做 `ReactLink `的插件来使其和双向数据绑定一样方便，但是考虑到这篇文章的目的，我们将会保持所有东西都直截了当。

如果你尝试在示例的当前版本中输入或者选中复选框，将会发现 React 会忽略你的输入。这是有意的，因为已经设置了 `input` 的 `value` 属性，使其总是与从 `FilterableProductTable` 传递过来的 `state` 一致。

让我们思考下我们希望发生什么。我们想确保无论何时用户改变了表单，都要更新 state 来反映用户的输入。由于组件只能更新自己的 state ， `FilterableProductTable `将会传递一个回调函数给 `SearchBar` ，此函数将会在 state 应该被改变的时候触发。我们可以使用 input 的 `onChange` 事件来监听用户输入，从而确定何时触发回调函数。`FilterableProductTable `传递的回调函数将会调用 `setState()` ，然后应用将会被更新。

虽然这听起来有很多内容，但是实际上仅仅需要几行代码。并且关于数据在应用中如何流动真的非常清晰明确。

## 就这么简单
希望以上内容让你明白了如何思考用 React 去构造组件和应用。虽然可能比你之前要输入更多的代码，记住，读代码的时间远比写代码的时间多，并且阅读这种模块化的清晰的代码是相当容易的。当你开始构建大型的组件库的时候，你将会非常感激这种清晰性和模块化，并且随着代码的复用，整个项目代码量就开始变少了 :)。