---
title: react.md
date: 2017-09-12 22:31:14
tags: react
categories: react
---

# 安装

1. cdn引入
2. `npm install -g create-react-app`

# 初步了解

react知识点大致分为以下方面：

1. [jsx](#jsx)
2. 元素渲染(#render)
3. 组件、props、state、生命周期(#component)
4. 事件处理(#event)
5. 高级渲染(#render)
6. 表单处理(#form)
7. 组合vs继承(#combile)

<h2 id="jsx">1. jsx</h2>

### 什么是jsx

jsx 是javascript语法的扩展，用来描述ui的组成，是React.CreateElement(Component,props,...children)方法的语法糖，返回js对象。

```
const Hello = <div name="hello">hello</div>


//等价于

const Hello = React.CreateElement("div",{name:"hello"},"hello")

```
推荐使用（）包裹起来，防止自动添加分号的bug.

```
const el =(
    <div>
        "hello"
    </div>
)

```


### jsx中的属性

jsx中的属性通过props传递，可大致分为以下四种情况

- javascript表达式

{}包裹javascript表达式

```
const el = <div name={2+2}>hello</div>//el.props = {name: 2+2}

```

- 字符串常量

```
const el = <div name="hello"></div>//props = {name:"hello"}

// 等价于

const el = <div name = {"hello"}></div>

```

- 没有给属性传值，默认为true(不推荐)

```
const el = <Fo name />// const el = <Fo name={true} />

```

- ...props

es6的语法

```

const props = {name: "hello",age:24};
const el = <Fo {...props} />

```


### jsx的子代(...children)

- 字符串常量

```
const el = <div>hello</div> // el.children = "hello"

```

- 嵌入的jsx元素

```
// el.children 以数组的形式返回，数组中的元素师React.CreateElement()方法返回的对象
const el = (
    <div>
        <h1>hello</h1>
        <h1></h1>
    </div>
);

```

- {}包裹的javascript表达式

你可以将任何 {} 包裹的 JavaScript 表达式作为子代传递。例如，下面这些表达式是等价的：

```

<MyComponent>foo</MyComponent>

<MyComponent>{'foo'}</MyComponent>

```

- 函数

通常情况下，插入 JSX 中的 JavsScript 表达式将被认作字符串、React 元素或这些内容的列表。然而，props.children 可以像其它属性一样传递任何数据，而不仅仅是 React 元素。例如，如果你使用自定义组件，则可以将调用 props.children 来获得传递的子代：

```

// Calls the children callback numTimes to produce a repeated component
function Repeat(props) {
  let items = [];
  for (let i = 0; i < props.numTimes; i++) {
    items.push(props.children(i));
  }
  return <div>{items}</div>;
}

function ListOfTenThings() {
  return (
    <Repeat numTimes={10}>
      {(index) => <div key={index}>This is item {index} in the list</div>}
    </Repeat>
  );
}

```

传递给自定义组件的子代可以是任何元素，只要该组件在 React 渲染前将其转换成 React 能够理解的东西。这个用法并不常见，但当你想扩展 JSX 时可以使用。


- 布尔值、Null 和 Undefined 被忽略

false、null、undefined 和 true 都是有效的子代，但它们不会直接被渲染。下面的表达式是等价的：

```

<div />

<div></div>

<div>{false}</div>

<div>{null}</div>

<div>{undefined}</div>

<div>{true}</div>

```

这样就可以方便的条件渲染 React 元素。当 showHeader 为 true 时，下面这个组件将只渲染一个 

```

<Header />

<div>
  {showHeader && <Header />}
  <Content />
</div>

```

值得注意的是，React 提供了一些 "falsy" 值 （即， 除了false 外，0，“”，null，undefined 和 NaN），它们依然会被渲染。例如，下面的代码不会像你预期的那样运行，因为当 props.message 为空数组时，它会打印 0：

```

<div>
  {props.messages.length &&
    <MessageList messages={props.messages} />
  }
</div>

```

要解决这个问题，请确保 && 前面的表达式始终为布尔值：

```

<div>
  {props.messages.length > 0 &&
    <MessageList messages={props.messages} />
  }
</div>

```

相反，如果你想让类似 false、true、null 或 undefined 出现在输出中，你必须先把它转换成字符串 :

```
<div>
  My JavaScript variable is {String(myVariable)}.
</div>

```

<h2 id="render">元素渲染</h2>

```
<div id="root"></div>// html

//js
reactDOM.render(
    el,
    document.getElementById("root")
)

```

调用ReactDOM.render()方法，页面需要添加一个根节点。渲染完成之后页面被容不会改变，想更新页面内容需要重新调用ReactDOM.render()方法或者使用state，并且页面更新只会更新修改的部分。

### ReactDOM

#### ReactDOM.render

ReactDOM.render(elment,container,callback)在指定的DOM容器内渲染React元素，渲染完成后有一个可选的回掉函数，如果提供回掉函数，将在渲染完成后执行。

#### unmountComponentAtNode()

ReactDOM.unmountComponentAtNode(container)

从DOM元素中移除已挂载的React组件，清除它的事件处理器和state。如果容器内没有挂载任何组件，这个函数什么都不会干。 有组件被卸载的时候返回true，没有组件可供卸载时返回 false。

#### findDOMNode()

ReactDOM.findDOMNode(component)
如果这个组件已经被挂载到DOM中，函数会返回对应的浏览器中生成的DOM元素 。 当你需要从DOM中读取值时，比如表单的值，或者计算DOM元素的尺寸，这个函数会非常有用。 大多数情况下，你可以添加一个指向DOM节点的引用，从而完全避免使用findDOMNode 这个函数. 当 render 返回 null 或者 false 时, findDOMNode 也返回 null.


### ReactDOMServer

可以在服务器端渲染组件    

```

import ReactDOMServer from 'react-dom/server'//es6
var ReactDOMServer = require('react-dom/server')//es5

```
#### renderToString()

ReactDOMServer.renderToString(element)
把一个React元素渲染为原始的HTML。这个方法最好只在服务端使用。React将会返回一段HTML字符串。你可以用这个方法在服务端生成HTML，并根据初始请求发送标记来加快页面的加载速度，同时让搜索引擎可以抓取你的页面来达到优化SEO的目的。

如果在一个已经有了服务端渲染标记的节点上调用 ReactDOM.render() ，React将保留该节点，仅作绑定事件处理，这会让你有一个非常高效的初次加载体验。

#### renderToStaticMarkup()

ReactDOMServer.renderToStaticMarkup(element)
类似 renderToString，但是不会创建额外的DOM属性，例如 data-reactid 这些仅在React内部使用的属性。如果你希望把React当作一个简单的静态页面生成器来使用，这很有用，因为去掉额外的属性可以节省很多字节。

### DOM元素

React实现了一套与浏览器无关的DOM系统，兼顾了性能和跨浏览器的兼容性。

在React中，所有的DOM特性和属性（包括事件处理函数）都是小驼峰命名法命名。比如说，与HTML中的tabindex属性对应的React实现命名则是tabIndex。aria-*和data-*属性是例外的，一律使用小写字母命名。

#### React和HTML DOM属性的区别

有许多的属性在React和Html之间行为是不一样的：

checked属性
input标签type属性值为checkbox或radio时，支持checked属性。这对于构建受控组件很有用。与之相对defaultChecked这是非受控组件的属性，用来设定对应组件首次加载时是否选中状态。

类名属性

在React中，使用className属性指定一个CSS类。这个特性适用于所有的常规DOM节点和SVG元素.

如果你在React中使用Web组件（这是一种不常见的使用方式），请使用class属性。

dangerouslySetInnerHTML函数#
dangerouslySetInnerHTML是React提供的替换浏览器DOM中的innerHTML接口的一个函数。一般而言，使用JS代码设置HTML文档的内容是危险的，因为这样很容易把你的用户信息暴露给跨站脚本攻击.所以，你虽然可以直接在React中设置html的内容，但你要使用 dangerouslySetInnerHTML 并向该函数传递一个含有__html键的对象，用来提醒你自己这样做很危险。例如：

```

function createMarkup() {
  return {__html: 'First &middot; Second'};
}

function MyComponent() {
  return <div dangerouslySetInnerHTML={createMarkup()} />;
}
```

htmlFor

因为在javascript中for是一个保留字，所以React元素使用 htmlFor代替。

onChange函数

onChange事件处理函数的表现正如你所期望的：无论form表单何时发生变化，这个事件都会被触发。我们特意不使用浏览器已有的默认行为，因为onChange在浏览器中的表现和这个名字不相称，而且React真实依靠这个事件实现了对用户输入的实时响应处理。

selected

option组件支持selected属性。你可以使用该属性设定组件是否选中的状态。这对构建受控组件很有用。

style属性

style属性接受一个键为小驼峰命名法命名的javascript对象作为值，而不是像css字符串。这和DOM中style属性接受javascript对象对象key的命名方式保持一致性，更高效而且能够防止跨站脚本（XSS）的安全漏洞。例如：

```
const divStyle = {
  color: 'blue',
  backgroundImage: 'url(' + imgUrl + ')',
};

function HelloWorldComponent() {
  return <div style={divStyle}>Hello World!</div>;
}
```

要注意，样式属性不会自动补齐前缀的。为了支持旧的浏览器，你需要手动支持相关的样式特性：

```
const divStyle = {
  WebkitTransition: 'all', // note the capital 'W' here
  msTransition: 'all' // 'ms' is the only lowercase vendor prefix
};

function ComponentWithTransition() {
  return <div style={divStyle}>This should work cross-browser</div>;
}

```

样式key使用小驼峰命名法是为了和JS访问DOM特性对对象的处理保持一致性。浏览器后缀除了ms以外，都应该以大写字母开头。这就是为什么WebkitTransition有一个大写字母W。

suppressContentEditableWarning

一般来说，当一个拥有子节点的元素被标记为contentEditable时，React会发出一个警告信息，因为此时contentEditable是无效的。这个属性会触发这样的警告信息。一般不要使用这个属性，除非你要构建一个类似Draft.js这样需要手动处理contentEditable属性的库。

value

input 和 textarea 组件都支持value属性。你可以使用该属性设置组件的值。这对构建受控组件非常有用。defaultValue属性对应的是非受控组件的属性，用来设置组件第一次加载时的值。

所有受支持的HTMl属性

React支持一下所有的属性，同时也支持所有的data-* 和 aria-*属性：

```
accept acceptCharset accessKey action allowFullScreen allowTransparency alt
async autoComplete autoFocus autoPlay capture cellPadding cellSpacing challenge
charSet checked cite classID className colSpan cols content contentEditable
contextMenu controls coords crossOrigin data dateTime default defer dir
disabled download draggable encType form formAction formEncType formMethod
formNoValidate formTarget frameBorder headers height hidden high href hrefLang
htmlFor httpEquiv icon id inputMode integrity is keyParams keyType kind label
lang list loop low manifest marginHeight marginWidth max maxLength media
mediaGroup method min minLength multiple muted name noValidate nonce open
optimum pattern placeholder poster preload profile radioGroup readOnly rel
required reversed role rowSpan rows sandbox scope scoped scrolling seamless
selected shape size sizes span spellCheck src srcDoc srcLang srcSet start step
style summary tabIndex target title type useMap value width wmode wrap
```

React也支持一下这些RDFa属性（有几个RDFa属性和HTML属性重叠，所以不包含在以下列表中）：

```
about datatype inlist prefix property resource typeof vocab
```

而且，React也支持下列非标准属性：

```
autoCapitalize autoCorrect for Mobile Safari.
color for <link rel="mask-icon" /> in Safari.
itemProp itemScope itemType itemRef itemID for HTML5 microdata.
security for older versions of Internet Explorer.
unselectable for Internet Explorer.
results autoSave for WebKit/Blink input fields of type search.

```

所有受支持的SVG属性

React支持以下的SVG属性：

```

accentHeight accumulate additive alignmentBaseline allowReorder alphabetic
amplitude arabicForm ascent attributeName attributeType autoReverse azimuth
baseFrequency baseProfile baselineShift bbox begin bias by calcMode capHeight
clip clipPath clipPathUnits clipRule colorInterpolation
colorInterpolationFilters colorProfile colorRendering contentScriptType
contentStyleType cursor cx cy d decelerate descent diffuseConstant direction
display divisor dominantBaseline dur dx dy edgeMode elevation enableBackground
end exponent externalResourcesRequired fill fillOpacity fillRule filter
filterRes filterUnits floodColor floodOpacity focusable fontFamily fontSize
fontSizeAdjust fontStretch fontStyle fontVariant fontWeight format from fx fy
g1 g2 glyphName glyphOrientationHorizontal glyphOrientationVertical glyphRef
gradientTransform gradientUnits hanging horizAdvX horizOriginX ideographic
imageRendering in in2 intercept k k1 k2 k3 k4 kernelMatrix kernelUnitLength
kerning keyPoints keySplines keyTimes lengthAdjust letterSpacing lightingColor
limitingConeAngle local markerEnd markerHeight markerMid markerStart
markerUnits markerWidth mask maskContentUnits maskUnits mathematical mode
numOctaves offset opacity operator order orient orientation origin overflow
overlinePosition overlineThickness paintOrder panose1 pathLength
patternContentUnits patternTransform patternUnits pointerEvents points
pointsAtX pointsAtY pointsAtZ preserveAlpha preserveAspectRatio primitiveUnits
r radius refX refY renderingIntent repeatCount repeatDur requiredExtensions
requiredFeatures restart result rotate rx ry scale seed shapeRendering slope
spacing specularConstant specularExponent speed spreadMethod startOffset
stdDeviation stemh stemv stitchTiles stopColor stopOpacity
strikethroughPosition strikethroughThickness string stroke strokeDasharray
strokeDashoffset strokeLinecap strokeLinejoin strokeMiterlimit strokeOpacity
strokeWidth surfaceScale systemLanguage tableValues targetX targetY textAnchor
textDecoration textLength textRendering to transform u1 u2 underlinePosition
underlineThickness unicode unicodeBidi unicodeRange unitsPerEm vAlphabetic
vHanging vIdeographic vMathematical values vectorEffect version vertAdvY
vertOriginX vertOriginY viewBox viewTarget visibility widths wordSpacing
writingMode x x1 x2 xChannelSelector xHeight xlinkActuate xlinkArcrole
xlinkHref xlinkRole xlinkShow xlinkTitle xlinkType xmlns xmlnsXlink xmlBase
xmlLang xmlSpace y y1 y2 yChannelSelector z zoomAndPan

```
<h2 id="component">3.组件和props、state、生命周期</h2>

### 组件定义的方式

#### 函数式

通过传入props参数

```
import React, { Component } from 'react';


function Child(props){
    return (
            <div>i am child</div>
        )
}

```

#### class

es6语法,组件中可以输出其它组件，react使用的是组合，类似Django使用的是继承来实现复杂的UI设计。

```
export default class Father extends Component{

    constructor(props) {
        super(props);
        
    }

    render() {
        return (
                <div>
                    <h2>{"i am class define"} </h2>
                    <Child/>
                </div>
            )
    }
}

```

### props

react是单向数据流（自上而下的数据流），使用props由父级组件向子组件传递属性，props必须象纯函数一样使用，不可以修改。子组件通过props.attr来使用

```
function Child(props){
    return (
            <div>{props.name}</div>
        )
}


export default class Father extends Component{

    constructor(props) {
        super(props);
        
    }

    render() {
        return (
                <div>
                    <h2>{"i am class define"} </h2>
                    <Child name={"hello,child"}/>
                </div>
            )
    }
}

```

### state和生命周期

#### state

state保存组件的内部状态，状态的改变可以重新渲染页面式改变的部分

```
class Child extends Component{
    constructor(props) {
        super(props);
        this.state = {
            time : new Date()
        }

        this.timer = setInterval(()=>{
            this.setState({
                time: new Date()
            })
        }, 1000);
    }

    render() {
        return (
            <div>
                    <div>{this.props.name}</div>
                    <div>{this.state.time.toLocaleTimeString()}</div>
                </div>
        )
    }
}

```

state的改变需要通过setState()方法来实现，方法可以有两种调用形式。

```
// 以对象的形式传入要修改的值，只会修改state中所传入的参数部分的值，其它值不变
this.setState({
    time: new Date()
 })
// 传入函数，函数接受两个可选参数，上一个状态和props
this.setState(
    (pre, props)=>{
        //console.log(props);//{name: 'hello,child'}
        return {time: new Date()}
    }
)

```

#### 生命周期

上面生成的计时器若是想再组件不在挂在的时候移除，则需要用到生命周期钩子,看着和vue的生命周期差不多

```
class Child extends Component{
    constructor(props) {
        super(props);
        this.state = {
            time : new Date()
        }

    }
    componentDidMount() {
        this.timer = setInterval(()=>{
            // this.setState({
            //     time: new Date()
            // })
            this.setState(
                (pre, props)=>{
                    //console.log(props);//{name: 'hello,child'}
                    return {time: new Date()}
                }
            )
        }, 1000);
    }
    componentWillUnmount() {
        clearInterval(this.timer);
    }
    render() {
        return (
            <div>
                    <div>{this.props.name}</div>
                    <div>{this.state.time.toLocaleTimeString()}</div>
                </div>
        )
    }
}

```

#### 收控组件和非受控组件

受控组件

在HTML当中，像input,textarea, 和 select这类表单元素会维持自身状态，并根据用户输入进行更新。但在React中，可变的状态通常保存在组件的状态属性中，并且只能用 setState(). 方法进行更新.即对每个状态传入一个函数来处理并阻止默认行为。

类似vue的数据绑定

```
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}

```

非受控组建

不为每个状态更新编写事件处理程序，而是使用ref获取dom引用。

```
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.input.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" ref={(input) => this.input = input} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}

```

<h2 id="event">事件处理</h2>

和html处理dom事件有以下不同

1. 使用驼峰命名法而非小写
2. 使用jsx语法传入的是一个函数作为事件处理者而非字符串
3. 不能使用return false来阻止默认行为，必须使用preventDefault
4. 类方法默认不会绑定this，可以使用以下方法

- 使用箭头函数

```
const el = (
        <div onClick={()=>this.handler()}>h</div>
    )
```

- 使用属性初始化方法

```
class El extends Component{
    constructor(){
        super(props);
        this.handler = this.handler.bind(this);
    }
    handler(){

    }
    render() {
        return (
            <div onClick={this.handler}></div>
            )
    }
}
```

<h2 id="render">高阶渲染</h2>

### 条件渲染

```
class Com extends Component{
    constructor(){
        super(props);
        this.state = {
            condition: false
        }
    }
    render() {
        return(
            <div>
                <h1 onClick={()=>this.setState(pre=>{return {condition: !pre.condition}})}>条件渲染</h1>
                (if this.state.condition &&
                        <div>condition is true</div>
                    )
            </div>
            )
    }
}


```

### 列表渲染

```
class Com extends Component{
    constructor(){
        super(props);
    }
    render() {
        const arr = [1,2,3];
        //key 用来对比更新，帮助识别react元素的变化，是react元素在兄弟元素之间独一无二的标识
        let list = arr.map((item,index)=>{
                return <div key={index}>{item}</div>
            })
        return(
            <div>
                <h1>列表渲染</h1>
                {list}
            </div>
            )
    }
}


```

### 状态提升

将子组件共用的数据状态提升至父组件

<h2 id="form">表单处理</h2>

参考受控组件和非受控组件

<h2 id="combile">组合vs继承</h2>

react使用的是组件的组合，即可以在组件中渲染其它组件，因此组件可以一步步划分成细小的组件，类似django则使用的是类的继承，通过创建并继承model类,来时现复杂的功能。react的理念应该是组件化，即将整个ui划分成细小的组件组合在一起，通过props自上而下传递数据，通过state来处理组件内部状态，实现更显react元素变化的部分。

# 深入

1. react元素如何渲染到页面
2. react中的事件处理









