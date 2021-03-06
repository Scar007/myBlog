---
title: React(1)
subtitle: React
date: 2018-10-7
categories: React
tags:
    - react
---
## React 基础篇
### 什么是React？
React是一个用于构建用户界面的JavaScript库，核心专注于视图，目的实现组件化开发。

### 组件化概念
我们可以很直观的将一个复杂的页面分割成若干个独立组件，每个组件包含自己的逻辑和样式，再将这些独立组件组合完成一个复杂的页面。这样既减少了逻辑复杂度，又实现了代码的重用

+ 可组合：一个组件可以和其他组件一起使用或者可以直接嵌套在另一个组件内部
+ 可重用：每个组件都具有独立功能，他可以被使用在多个场景中
+ 可维护：每个小的组件仅仅包含自身的逻辑，更容易被理解和维护

### 跑通React开发环境
```javascript
npm install create-react-app -g
create-react-app <项目名>
cd <项目名>
npm start
```
默认会自动安装React，React由两部分组成，分别是：
+ react.js 是React的核心库
+ react-dom.js 是提供与DOM相关的功能，会在window下增加ReactDOM属性，内部比较重要的方法是render，将react元素或者react组件插入到页面中。

### 简介JSX
是一种JS和HTML混合的语法，将组织的结构，数据甚至样式都聚合在一起定义组件，会编译成普通的JavaScript。需要注意的是JSX并不是HTML，在JSX中属性不能包含关键字，像class需要写成className，for需要写成htmlFor，并且属性名需要采用驼峰命名！

### createElement
JSX其实只是一种语法糖，最终会通过babel转译成createElement语法，以下代码等价
```javascript
ReactDOM.render(<div>高<span>佳睿</span></div>);
ReactDOM.render(React.createElement("div",null,"高",React.createElement("span",null,"佳睿")));
```
我们一般使用React.createElement来创建一个虚拟dom元素。

### react元素/JSX元素
```javascript
function ReactElement(type,props) {
    this.type = type;
    this.props = props;
};
let React = {
    createElement(type,props = {},...childrens){
        childrens.length === 1 ? childrens = childrens[0]: viod 0
        return new ReactElement(type,{...props,children:childrens})
    }
};
```
ReactElement就是虚拟dom的概念，具有一个type属性代表当前的节点类型，还有节点的属性props

### 模拟render实现
```javascript
let render = (eleObj,container)=>{
    // 先取出第一层 进行创建真实dom
    let {type,props} = eleObj;
    let elementNode = document.createElement(type); // 创建第一个元素
    for(let attr in props){ // 循环所有属性
        if(attr === 'children'){ // 如果是children表示有嵌套关系
            if(typeof props[attr] == 'object'){ // 看是否是只有一个文本节点
                props[attr].forEach(item=>{ // 多个的话循环判断 如果是对象再次调用render方法
                    if(typeof item === 'object'){
                        render(item,elementNode)
                    }else{ //是文本节点 直接创建即可
                        elementNode.appendChild(document.createTextNode(item));
                    }
                })
            }else{ // 只有一个文本节点直接创建即可
                elementNode.appendChild(document.createTextNode(props[attr]));
            }
        }else if(attr === 'className'){ // 是不是class属性 class 属性特殊处理
            elementNode.setAttribute('class',props[attr]);
        }else{
            elementNode.setAttribute(attr,props[attr]);
        }
    }
    container.appendChild(elementNode)
};
```

### JSX表达式的用法
+ 1）可以放JS的执行结果
+ 2）如果换行需要用（）包裹jsx代码
+ 3）可以把jsx元素当做函数的返回值
+ 4）<{来判断是表达式还是js
```javascript
import React from 'react';
import ReactDOM from 'react-dom';

function toResult({name,age}) {
    return <span>今年{name},{age}岁了!</span>
}
let arrs =  [{name:'kris',age:28},,{name:'高佳睿',age:23}];
ReactDOM.render(<div>
    {arrs.map(((item,index)=>(
        typeof item==='object'?<li key={index}>{toResult(item)}</li>:null
    )))}
</div>,document.getElementById('root'));
```
> 注意：null也是合法元素，循环时需要带key属性

### JSX属性
在jsx中分为普通属性和特殊属性，像class要写成className，for要写成htmlFor，style要采用对象的方式dangerouslyInnerHTML插入html

### 组件的特别声明方式
react元素是是组件组成的基本单位
首字母必须大写,目的是为了和JSX元素进行区分 组件定义后可以像JSX元素一样进行使用 每个组件必须返回唯一的顶级JSX元素 可以通过render方法将组件渲染成真实DOM

### 组件的两种定义方式
react怎么区分是组件还是jsx元素？组件名需要开头大写，react组件当作jsx来进行使用

+ 第一种方式是函数声明
```javascript
function Build(props) {
  return <p>{props.name} {props.age}</p>
}
render(<div>
  <Build name={preson1.name} age={person2.age}/>
  <Build {...person2} />
</div>,window.root);
```
+ 第二种方式是类声明
```javascript
class Build extends Component{
  render(){
      let {name,age} = this.props;
      return <p>{name} {age}</p>
  }
}
```
> 注意：类声明有状态，this，和声明周期

### 组件中属性和状态的区别
组件的数据来源有两个地方 props 外界传递过来的(默认属性，属性校验) state 状态是自己的,改变状态唯一的方式就是setState 属性和状态的变化都会影响视图更新

### 绑定事件
给元素绑定事件，事件绑定方式
```javascript
class Clock extends Component {
    constructor(){
        super();
        this.state = {date:new Date().toLocaleString()}
    }
    componentDidMount(){ //组件渲染完成，当渲染后会自动触发此函数
        this.timer = setInterval(()=>{ // 箭头函数 否则this 指向的是window
            this.setState({date:new Date().toLocaleString()})
        },1000);
    }
    componentWillUnmount(){ //组件将要卸载，当组件移除时会调用
        clearInterval(this.timer); //一般在这个方法中 清除定时器和绑定的事件
    }
    destroy=()=>{ //es7 箭头函数
        // 删除某个组件
        ReactDOM.unmountComponentAtNode(window.root);
    }
    render(){
        // 给react元素绑定事件默认this是undefined,bind方式 在就是箭头函数
        return <h1 onClick={this.destroy}>{this.state.date}</h1>
    }
}
```
> 注意：// 执行顺序 constructor -> render -> componentDidMount -> setState-> render - onClick-> unmountComponentAtNode -> componentWillUnmount -> clearInterval ReactDOM.render(,window.root); 给jsx元素绑定事件要注意事件中的this指向，事件名采用 on+"开头大写事件名"的方式

### 属性校验，默认属性
```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import PropTypes from 'prop-types'; //引入属性校验的模块
class School extends React.Component{ // 类上的属性就叫静态属性
    static propTypes = { // 校验属性的类型和是否必填
        age:PropTypes.number.isRequired, // 支持的类型可以参考prop-types的readme文件
    };
    static defaultProps = { // 先默认调用defaultProps
        name:'高佳睿',
        age:18
    }; // 默认属性
    constructor(props){ //如果想在构造函数中拿到属性需要通过参数的方式
         //不能在组件中更改属性 不能修改属性*
        super();
    }
    render(){
        return <h1>{this.props.name} {this.props.age}</h1>
    }
}
propTypes和defaultProps名字不能更改，这是react规定好的名称
```

### 状态的使用 
```javascript
class Counter extends Component{
    constructor(){
        super();
        this.state = {count:0}
    };
    handleClick = ()=>{
        // setState方法会进行合并 setState有两种写法 一种是对象一种是函数
        /*this.setState({count:this.state.count+1});
          this.setState({count:this.state.count+1});*/
        //this.setState((prevState)=>({count:prevState.count+1})); //如果返回的就是一个对象可以用小括号包裹
        //this.setState((prevState)=>({count:prevState.count+1}));
        // 下一个状态是依赖于上一个状态时需要写成函数的方式
        this.setState({count:this.state.count+1},function () {
            this.setState({count:this.state.count+1});
        }); // 这个写法等同于 this.setState((prevState)=>({count:prevState.count+1}));
    };
    render(){
        console.log('render');
        return (
            <p>
                {this.state.count}
                <button onClick={this.handleClick}>+</button>
            </p>
        )
    }
}
ReactDOM.render(<Counter/>,window.root);
```
如果设置多个状态setState会合并，如果下一个状态依赖于上一个状态，需要写成函数的方式

### 复合组件
复合组件就是将多个组件进行组合，结构非常复杂时可以把组件分离开<br>
父子组件的通信
```javascript
class Panel extends Component{
    render(){
        let {header,body} = this.props;
        return (
            <div className="container">
                <div className="panel-default panel">
                    <Header head={header}></Header>
                    <Body b={body}/>
                </div>
            </div>
        )
    }
} // react种需要将属性一层层向下传递 单向数据流
class Body extends Component{
    render(){return (<div className="panel-body">{this.props.b}</div>)}
}
class Header extends Component{
    render(){return (<div className="panel-heading">{this.props.head}</div>)}
}
let data = {header:'灰常美',body:'炒鸡美'};
ReactDOM.render(<Panel {...data}/>,window.root);
```
子父组件的通信 通过父亲传递给儿子一个函数，儿子调用父亲的函数将值传递给父亲,父亲更新值，刷新视图
```javascript
class Panel extends Component{
    constructor(){
        super();
        this.state = {color:'primary'}
    }
    changeColor=(color)=>{ //到时候儿子传递一个颜色
        this.setState({color});
    };
    render(){
        return (
            <div className="container">
                <div className={"panel-"+this.state.color+" panel"}>
                    <Header head={this.props.header}
                            change={this.changeColor}
                    ></Header>
                </div>
            </div>
        )
    }
}
class Header extends Component{
    handleClick = ()=>{
        this.props.change('danger'); //调用父亲的方法
    };
    render(){return (
        <div className="panel-heading">
        {this.props.head} <button className="btn btn-danger" onClick={this.handleClick}>改颜色</button>
        </div>)}
}
```

### 受控组件，非受控组件
受状态控制的组件，必须要有onChange方法，否则不能使用 受控组件可以赋予默认值（官方推荐使用 受控组件） 实现双向数据绑定
```javascript
class Input extends Component{
    constructor(){
        super();
        this.state = {val:'100'}
    }
    handleChange = (e) =>{ //e是事件源
        let val = e.target.value;
        this.setState({val});
    };
    render(){
        return (<div>
            <input type="text" value={this.state.val} onChange={this.handleChange}/>
            {this.state.val}
        </div>)
    }
}
```
受控组件
```javascript
class Sum extends Component{
    constructor(){
        super();
        this.state = {a:1,b:1}
    }
    // key表示的就是当前状态改的是哪一个
    // e表示的是事件源
    handleChange(key,e){ //处理多个输入框的值映射到状态的方法
        this.setState({
            [key]:parseInt(e.target.value)||0
        })
    }
    render(){
        return (
            <div>
                <input type="number" value={this.state.a} onChange={e=>{this.handleChange('a',e)}}/>
                <input type="number" value={this.state.b} onChange={e=>{this.handleChange('b',e)}}/>
                {this.state.a+this.state.b}
            </div>
        )
    }
}
```
非受控组件
```javascript
class Sum extends Component{
    constructor(){
        super();
        this.state =  {result:''}
    }
    //通过ref设置的属性 可以通过this.refs获取到对应的dom元素
    handleChange = () =>{
        let result = this.refs.a.value + this.b.value;
        this.setState({result});
    };
    render(){
        return (
            <div onChange={this.handleChange}>
                <input type="number" ref="a"/>
                {/*x代表的真实的dom,把元素挂载在了当前实例上*/}
                <input type="number" ref={(x)=>{
                    this.b = x;
                }}/>
                {this.state.result}
            </div>
        )
    }
}

```








