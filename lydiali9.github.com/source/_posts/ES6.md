title: ECMAScript 6
date: 2018-02-25 12:09:01
tags: ['ECMAScript 6', 'ES6']
categories: ['ECMAScript 6', 'ES6']
---
ECMAScript 和 JavaScript 的关系是，前者是后者的规格，后者是前者的一种实现（另外的 ECMAScript 方言还有 Jscript 和 ActionScript）。日常场合，这两个词是可以互换的。

#### 声明方式 - let ／ const
1. let - 变量不允许重复声明  允许重复赋值 可以防治变量泄露
2. const - 常量 变量不允许重复声明，也不允许重复赋值

#### 编码
1. charCodeAt 获取编码
2. String.fromCharCode(编码) 能让编码转成字符

#### 字符串
1. repeat和includes
repeat 接受1个参数 参数就是重复几次咱们的字符串
includes 有点像indexOf
查找会返回 布尔值
第一个参数 是要找的内容
【第二个参数】 从第几位开始，默认从0开始

2. startsWith 起始
开头如果是你放入的第一个参数 就是true 不是就false
【第二个参数】 从第几位开始，默认从0开始

3. endsWith 结束位置
第二个参数 从第几位结束

#### 模板字符串 
1. 传统js语言
```javascript
$('#result').append(
  'There are <b>' + basket.count + '</b> ' +
  'items in your basket, ' +
  '<em>' + basket.onSale +
  '</em> are on sale!'
);

$('#result').append(`
  There are <b>${basket.count}</b> items
   in your basket, <em>${basket.onSale}</em>
  are on sale!
`);
```

2. ${}字符串
```javascript
let a = '5';
let b = '10';
console.log(`${a + b}`)
```

#### 数组find
find 第一个参数 循环出来 数组所有的内容
     第二个参数 循环出来 下标
     第三个参数 循环出来 数组本身
1. find(x)
```javascript
[1, 4, -5, 10].find(function(x) {
    console.log(x);
});
// 1 4 -5 10
[1, 4, -5, 10].find(x => {
    console.log(x);
});
```
2. find(x, y)
```javascript
[1, 4, -5, 10].find(function(x, y) {
    console.log(x, y);
});
//1  0
//4  1
//-5 2
//10 3

[1, 4, -5, 10].find((x, y) => {
    console.log(x, y);
});
```
3. find(x, y, z)
```javascript
[1, 4, -5, 10].find(function(x, y, z) {
    console.log(z[y]);
});
// 1 4 -5 10

[1, 4, -5, 10].find((x, y, z) => {
    console.log(x, y, z);
});
```

#### 函数
1. 函数一
```javascript
x = x => x;
//如上表达式相当于如下方法
var x = function(x){
    return x;
};
// 如上两种写法，都不能再定义前去调用
```

2. 函数二
```javascript
document.onclick = function(){
    document.body.style.background = 'black';
}

document['onclick'] = () => {
    document.body.style.background = 'black';
}
```

3. 函数三
```javascript
(function(){
    alert(1);
})()

let x = 10;
((x)=>{
    console.log(x)
})(x)
```

#### 延展参数
延展参数 如果传了参数 那么就执行传入的参数 如果没传参数 那么参数就为延展的值

1. 延展参数一（概念同PHP方法）
```javascript
function show(x=5,y=[1,2,3]){
  console.log(y)
};
show();
```

2. 延展参数二
```javascript
var obj = {a : 3}
function show(x={a:5,b:3}){
  //var x = x || 5;
  console.log(x.b)
};
show();
```

* 延展参数 可以延展任何类型 array  string number json

#### 扩展运算符
1. 扩展运算符一
```javascript
function show(){
  console.log(arguments); // [1, 2, 3, 4]
};

function show(...x){
  console.log(x)  // [1, 2, 3, 4]
};

function show(...x){
  //arguments.push(5); 报错
  x.push(5); // 正确
  console.log(x)  // [1, 2, 3, 4, 5]
};

function show(y,...x){
  console.log(x)  // [2, 3, 4]
};

function show(y=[1,23,3],...x){
  console.log(x) // [2, 3, 4]
};
show(1,2,3,4);
```

2. 扩展运算符二
```javascript
var a = [1,3,4,4];
var b = [2];
var c = [3];
console.log(a.concat(b.concat(c)));
console.log([...a,...b,...c]);
```

3. 扩展运算符三
```javascript
// 输出重复的5个<div></div>
let a = '<div></div>';
document.write(a.repeat(5));

// 实现如上功能方法一
var c = [];
var d = document.getElementsByTagName('div');
for(var i = 0;i < d.length; i++) {
  c.push(d[i]);
};
console.log(c);

// 实现如上功能方法二
console.log([...document.querySelectorAll('div')])
```


#### 生成器函数
function* 函数名(){
}

1. 函数名.next();

```javascript
function* show(){
  alert(1);
};
console.log(show().next());
```


2. yield 有点类似于return
```javascript
function* show(){
  yield function() {
    console.log(1)
  }
};
show().next().value(); // 1
```

3. yield 有点类似于return
```javascript
function* show(){
  yield function() {
    console.log(1)
  }

  yield function() {
    console.log(1)
  }  
};
var k = show();
k.next().value(); // 1
k.next().value(); // 2
```

4. yield 有点类似于return
```javascript
function* show() {
    yield ()=> {
      console.log(1)
    }

    yield function() {
      console.log(2);
    }

    yield ()=> {
      document.body.style.background = 'black';
    }
  };

  var k = show();

  document.onclick = x => {
    k.next().value(); // 1 2 背景变黑
  }
```

#### Json中的get和set方法
1. set
```javascript
json = {
  a(){
    console.log(1);
  }
};
json.a(); // 1
```

2. set和get
```javascript
var json = {};
json = {
  set Leo(x){
    console.log(x)
  },
  get Leo(){
    console.log(3);
  }
};
json.Leo; // Leo赋值输出给的值，反之是输出get中的方法
console.log(json) 
```

3. set和get
```javascript
var json = {};
json = {
  set Leo(x){
    console.log(x)
  },
  get Leo(){
    return {
      get name(){
        console.log(3);
      }
    }
  }
};
console.log(json.Leo.name); // 3
```

#### 块级作用域
1. 块级作用域一（{}块状 块级作用域 可以解决 密封空间问题）
```javascript
let allInput = document.getElementsByTagName('input');

for(var i = 0;i < allInput.length;i++){
  allInput[i].onclick = function(){
    alert(i)
  }
}
// 只输出3
```

2. 块级作用域二
```javascript
let allInput = document.getElementsByTagName('input');

for(var i = 0;i < allInput.length;i++){
  (function(x){
    allInput[x].onclick = function(){
      alert(x);
    }
  })(i)
} 
// 输出0 1 2
```

3. 块级作用域三(用let可以解决块级作用域的问题)
```javascript
//<input value='1' type='button' class='active'>
// <input value='2' type='button'>
// <input value='3' type='button'>
let allInput = document.getElementsByTagName('input');

for(let i = 0;i < allInput.length;i++){
  allInput[i].onclick = function(){
    for(var j = 0;j < allInput.length;j++){
      allInput[j].className = '';
    }
    allInput[i].className = 'active';
  }
}
```

#### 结果赋值(值和结构一样就可以)
1. 数组结果赋值一
```javascript
//var a = 10,b = 20,c = 30;
let [a,b,c] = [10,20,30];
console.log(a,b,c);
```

2. 数组结果赋值二
```javascript
//var a = 10,b = 20,c = 30;
let [a,[b,f],c] = [10,[20,40],30];
console.log(a,b,c,f) // 10 20 30 40
```

3. json结果赋值
```javascript
var json = {'a':20,'b':[30,20]};
let {a,b} = json;
console.log(a,b); // 20 [30, 20]
```

4. 结果集赋值(简化代码结构)
```javascript
function show(a=[30,50,80,100]){
  //console.log(a);
  /*
  var b = a[0];
  var c = a[1];
  var d = a[2];
  var e = a[3];
  */
  var [b,c,d,e] = a;
  console.log(b,c,d,e)
};

show()
```

5. 结构赋值函数
```javascript
var data = {
  'ok':1,
  'data':{
    'top':[1,2,3],
    'center':[2,3,4]
  },
  'mis':'请求成功'
};

let {ok, data, mis} = data;
```

#### bind
1. jquery -> bind 绑定事件 addevent

2. bind -> call
任何函数都可以用 call 调用自己 call 的第一个参数 就是 函数的this 第二个参数之后就是函数的行参

3. call
```javascript
alert.call(window,1) // 1
```

4. call
```javascript
setTimeout.call(window,()=>{
  alert.call(window,1)   // 1
},1000);
```

5. bind
```javascript
function show() {
  console.log(1);
}

console.log(show);
/*
输出： 
function show() {
  console.log(1);
}
 */

// 同上
function show() {
  console.log(1);
}

console.log(show.bind(1));
```

6. bind
```javascript
function show() {
  console.log(this); // this = 1
}

show.bind(1)(); // 1
```

7. bind
```javascript
function show(x, y, z) {
  console.log(this+x); 
}

show.bind(1, 2, 3, 4)(); // 3
```

8. bind
```javascript
function show(x, y, z) {
  console.log(x);
}

show.bind(1)(1); // 1
```

9. bind
```javascript
function show(x, y, z) {
  console.log(x);
}

show.bind(1, 2)(1); // 2
```

10. bind
```javascript
function show(x, y, z) {
  console.log(x, y);
}

show.bind(1, 2)(1); // 2 1 参数先执行（1， 2）后执行（1）
```

*  任何函数都可以用`bind`调用自己,`bind`的第一个参数就是函数的`this` 第二个参数之后就是函数的行参 但是不会调用自己
* constructor 可以精准的确定是什么类

#### 对象的扩展
##### JS面向对象
1. 原型：为每一个实例对象存储共享的方法和属性，每一个函数都有一个prototype原型属性，这个属性是一个指针，指向一个对象。
2. 原型链：让一个引用类型继承另一个引用类型的属性和方法。

3. 自定义原型方法
```javascript
function show(){
  //console.log(1)
};
show.prototype.a = function(){
  alert(1);
}
var c = new show()
c.a();
//var c = show;
//console.log(c.prototype)  
```

##### ES6面向对象
1. 语法
```javascript
class functionName {
 constructor(){
 
 }
}
```

2. 传参规则
```javascript
class a {
  constructor(x,y,z){
    console.log(x,y,z) //11,[1,2,3],{a:50}
  }
};

new a(11,[1,2,3],{a:50});
```

3. 定义方法
```javascript
class a {
    constructor(x,y,z){
      console.log(x,y,z)
    }
    getNum(){
      console.log(1); // 1
    }
  };

  new a(11,[1,2,3],{a:50}).getNum();
```


4. 定义方法传参
```javascript
class a {
    constructor(x,y,z){
      console.log(x,y,z)
    }
    getNum(x){
      console.log(x); // 1
    }
  };

  new a(11,[1,2,3],{a:50}).getNum(1);
```

4. his指向
```javascript
class a {
    constructor(x,y,z){
      console.log(x,y,z)
      this.x = 10;
      //console.log(this.x)
    }
    ffff(x){
      console.log(this.x); // 10 this=a
    }
  };

  //new a(11,[1,2,3],{a:50}).ffff(1);
```

#### Demo
```javascript
<!-- <body>
  <div id='box'>
    <input type='button' value='1' class='active'>
    <input type='button' value='2'>
    <input type='button' value='3'>
    <div>aaaaa</div>
    <div>bbbbb</div>
    <div>ccccc</div>
  </div>
  <div id='ccc'>
    <input type='button' value='1' class='active'>
    <input type='button' value='2'>
    <div>aaaaa</div>
    <div>ccccc</div>
  </div>
</body> -->
  class tab {
  constructor(x){
    this.node = document.getElementById(x);
    this.allIput = this.node.getElementsByTagName('input');
    this.allDiv = this.node.getElementsByTagName('div');
    this.index = 0;

    this.init();
  }
  init(){
    for(let i = 0;i < this.allIput.length;i++){
      this.allIput[i].onclick = function(){
        this.hide();
        this.show(i);
      }.bind(this);
    }
  }
  hide(){
    for(let i = 0;i < this.allIput.length;i++){
      this.allIput[i].className = '';
      this.allDiv[i].style.display ='none';
    }
  }
  show(index){
    this.allDiv[index].style.display ='block';
    this.allIput[index].className ='active';
  }
}

class autoTab extends tab{
  constructor(x){
    super(x);
  }
  autoPlay(){
    setInterval(()=>{
      this.index++;
      if(this.index == this.allIput.length)this.index = 0;
      this.hide();
      this.show(this.index);
    },1000);
  }
}

new tab('box')
new autoTab('ccc').autoPlay();
```


#### 面向对象继承
1. 继承一
```javascript
class show {
  constructor(l) {
    this.x = l;
  }
  getNum(){
    alert(this.x);
  }
};

new show(1).getNum(); //1
```

2. 继承二
```javascript
aaa.prototype = new bbb();
```

3. 继承三
```javascript
class show {
  constructor(l) {
    this.x = l;
  }
  getNum(){
    alert(this.x);
  }
};
class sub extends show {
  constructor(l){
    super(l) //相当于拿到父级的this
  }
}
new sub(1).getNum(); // 1
```

#### CSS JQuery
1. jquery
```javascript
$(‘.div’).addClass();
$(‘#div’).removeClass();
$(‘div’).bind(‘click’,function(){

})
```

2. ES6
```javascript
$('#div1').css({
  'width':'300px',
  'height':'100px',
  'transition':'1s',
  'background':'green'
})  
```

#### 模版(k可以放入函数及传参)
```javascript
function show(n){
  var json = {a: 10, b: 20};
  return json;
}

var string = `json is ${show().a}`;
console.log(string); //json is 10
```

#### map
1. map 一
```javascript
let arr = [1, 10, 99];
arr.find((x, y, z) => {
  console.log(x, y, z);
});
//  输出：
// 1 0  
// 10 1
// 99 2
// 返回：
// undefined
```

2. map 二（同上）
```javascript
let arr = [1, 10, 99];
arr.find((x, y, z) => {
  console.log(x, y, z);
});
//  输出：
// 1 0  
// 10 1
// 99 2
// 返回：
// -1
```

3. map 三
```javascript
let arr = [1, 10, 99];
arr.find((x, y, z) => {
  console.log(x, y, z);
});
//  输出：
// 1 0  
// 10 1
// 99 2
// 返回：
// [undefined, undefined, undefined]
```

* map 用法和find findindex一样，只不过返回的值不同，是一个大的数组

#### 标签模板
1. 标签模板一
```javascript
alert`1`;
```

2. 标签模板二
```javascript
var jsonData = {
  'topValue':['李万宇','申瑞','王凯'],
  'bottomInner':['瘦了','美了','挣钱了']
}

createNode = json => `<div class=outNode>
  ${json.topValue.map((v)=>
    `<input type=button value=${v}>`).join('')}
  ${json.bottomInner.map((v)=>
    `<div>${v}</div>`).join('')}
  </div>`;

document.body.innerHTML = createNode(jsonData)
```


#### String.raw``(可以取消转义)
```javascript
console.log('L\\nE\\nO')

// 输出换行，实现同上功能
console.log(String.raw`L\nE\nO`);
```

#### number
1. 判断整数 true false
```javascript
var number = 1.00001
var string = String(number);
console.log(string.indexOf('.')!=-1?'不是整数':'是整数')

//实现如上功能
console.log(Number.isInteger(1.1+2.2)?'是整数':'不是整数');
```

2. isNaN parseInt parseFloat
```javascript
console.log(Number.isNaN('218s0')); //false

console.log(Number.parseInt == parseInt) // true
```

3. Math
```javascript
console.log(Math.pow(4,4)) //64
```

#### 常用方法
1. in (‘属性’ 是否在 该对象上)
```javascript
var json = {
  'leo':18,
  'hehe':3
};
console.log('o' in json); //true


document.addEventListener('click',function(){
  alert(1);
},false);   // 1 IE不支持

// 兼容IE方法修改如下
function addEvent(obj,event,fn){
  'attachEvent' in window?obj.attachEvent('on'+event,fn):obj.addEventListener(event,fn);
  /*
  if(obj.attachEvent){
    obj.attachEvent('on'+event,fn);
  }
  else{
    obj.addEventListener(event,fn);
  }
  */
}
addEvent(document,'click',function(){
  alert(1);
})
```

2. in 
```javascript
var arr = [5,6,9,4];
<!-- arr[0]
arr[1]
arr[2]
arr[3] -->
console.log(0 in arr); // true
console.log(4 in arr); // false
```

3. for in (下表)
```javascript
var arr = [5,6,9,4];
for(var i in arr){
  console.log(i)
}
// 0 1 2 3 


var json = {a : 10};
for(var i in json){
  console.log(i)
}
// a
```

4. for of (值)
```javascript
var arr = [5,6,9,4];
for(var i of arr){
  console.log(i)
}
// 5 6 9 4
```


4. set(元素不重复)
```javascript
var set = new Set();
set.add(1); // 添加数据
set.add(2);
set.add(3);
set.add(4);
set.add(4);
set.add(4);
set.add(4);
set.add(4);
console.log(set)
//console.log(Set.prototype) //查看所有的方法
// {1, 2, 3, 4}

// has查找数据
var set = new Set();
set.add(1);
set.add(2);
set.add(3);
set.add(4);
set.add(4);
set.add(4);
set.add(4);
set.add(4);
console.log(set.has(5)) // false

// keys
var set = new Set();
set.add(1);
set.add(2);
set.add(3);
set.add(4);
set.add(4);
set.add(4);
set.add(4);
set.add(4);
console.log(set.keys()) // [0:1, 1:2, 2:3, 3:4]
console.log(set.values()) // {1, 2, 3, 4}

for(var i of set.values()){
  console.log(i)
}
// 1 2 3 4


// size 类似于length 指的是长度
var set = new Set();
set.add(1);
set.add(2);
set.add(3);
set.add(4);
set.add(4);
set.add(4);
set.add(4);
set.add(4);
console.log(set.size) // 4

// delete 放入你删除的值
var set = new Set();
set.add(1);
set.add(2);
set.add(3);
set.add(4);
set.add(4);
set.add(4);
set.add(4);
set.add(4);
set.delete(4);
console.log(set.values()) // 

for(var i of set.values()){
  console.log(i)
}
// 1 2 3 

// clear 直接清除
var set = new Set();
set.add(1);
set.add(2);
set.add(3);
set.add(4);
set.add(4);
set.add(4);
set.add(4);
set.add(4);
set.delete(4);
set.clear();
console.log(set.values())

for(var i of set.values()){
  console.log(i)
}
// clear

// entries
var set = new Set();
set.add(1);
set.add(2);
set.add(3);
set.add(4);
set.add(window);
set.add(4);
set.add(4);
set.add(4);

console.log(set.entries()); // {[1, 1], [2, 2], [3, 3], [4, 4]}


//array
var arr = [1,2,3,4,5];
var arr2 = [1,2,3,4,5,6,7,7];
var set = new Set([...arr,...arr2]);

var arr3 = [...set];
console.log(arr3); // 1 2 3 4 5 6 7
```


#### Map (不允许重复存key)
```javascript
let arr = ['button','text','button'];
var a = arr.map((x,y,z)=>{
  var oBtn = document.createElement('input');
  oBtn.type=x;
  return oBtn;
});


// set get
const m = new Map();
const o = {p: 'Hello World'};

m.set(o, 'content')
m.get(o) // "content"

m.has(o) // true
m.delete(o) // true
m.has(o) // false


// has size
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);

map.size // 2
map.has('name') // true
map.get('name') // "张三"
map.has('title') // true
map.get('title') // "Author"


// forEach
const items = [
  ['name', '张三'],
  ['title', 'Author']
];

const map = new Map();

items.forEach(
  ([key, value]) => map.set(key, value)
);

// map (for of)
var mapNode = new Map();
var json = {
  '王艺蒙':'瘦了',
  '申瑞':'有钱了'
}
for(var i in json){
  mapNode.set(i,json[i]);
}

var json2={}
for(let [k,v] of mapNode){
  json2[k] = v;
}
console.log(json2);
```

#### Promise
1. Promise 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。

```javascript
new Promise(function(Resolved,reject) {
  Resolved(); // 只要成功，后面的方法不执行
  reject();
}).then(function() {
  alert('成功')
}, function() {
  alert('失败')
})
```

* one  只执行一次
* then(成功函数 , 失败的函数)
* then(成功函数).catch( 失败的函数)

```javascript
// 1
new Promise(function(Resolved,reject) {
  Resolved("data");
  reject();
}).then(function(x) {
  console.log(x); // data
}).catch(function() {
  console.log("失败");
})

// 2
function showPromise(){
  return new Promise((resolve,reject) => {
    resolve("success");
  }).then((x) => {console.log(x)}, () => {

  })
}
showPromise();

// 3
function showPromise(res){
  return new Promise((resolve,reject)=>{
    resolve(res)
  }).then((x)=>{console.log(x)}, ()=>{

  })
}
showPromise("success");

// 4
new Promise(function(Resolved,reject){
  Resolved('hehe');
}).then(function(x){
  alert(x); //hehe
  return 'ashkdajs';
}).then(function(x){
  alert(x); // ashkdajs
  return '101010'
}).then(function(x){
  alert(x); // 101010
})
```

* promise 厉害之处就是能无限的回调


2. Demo
```javascript
//<input id='ipt' type='button' value='dddd'>
//<div id='div1'></div>

let index = 0;
ipt.onclick = x =>{
  index++;
  new Promise((succ,error)=>{
    index%2==1?succ():error();
  }).then(()=>{
    div1.style.display = 'none';
  },()=>{
    div1.style.display = 'block';
  })
};
```


3. catch
```javascript
new Promise((succ,error)=>{
  error('asdasda');
}).then(()=>{}).catch((x)=>{
  console.log(x); // asdasda
  return 'asdjkas';
}).then((f)=>{
  console.log(f); //asdjkas
});
```

4. error warn
```javascript
console.error();
console.warn();
```

4. throw
```javascript
new Promise((succ,error)=>{
  succ('123');
}).then((x)=>{
  throw x; // 抛出异常 下面的代码不执行
  return x+1
}).then((f)=>{
  console.log(f);
}).then(()=>{

})
```

4. race 竞速方法
```javascript
Promise.race([new Promise((succ,error)=>{
  setTimeout(succ,1000,'1');
}),new Promise((succ,error)=>{
  setTimeout(succ,1200,'2');
}),new Promise((succ,error)=>{
  setTimeout(succ,1400,'3');
})]).then((x)=>{
  console.log(x+'suss'); // 1succ

},(x)=>{
  console.log(x+'error')
})



Promise.race([new Promise((succ,error)=>{
  setTimeout(succ,1000,'1');
}),new Promise((error,error)=>{
  setTimeout(succ,1200,'2');
}),new Promise((succ,error)=>{
  setTimeout(succ,1400,'3');
})]).then((x)=>{
  console.log(x+'suss');

},(x)=>{
  console.log(x+'error')  // 1error
})
```

* race ->只看第一个promise 成功就成功 ，失败就失败

4. all 规整方法
```javascript
Promise.all([new Promise((succ,error)=>{
  setTimeout(succ,1000);
}),new Promise((succ,error)=>{
  setTimeout(succ,2000);
}),new Promise((succ,error)=>{
  setTimeout(succ,1000);
})]).then(()=>{
  console.log(1)    // 1
}).catch(()=>{
  console.log(2)
})


Promise.all([new Promise((succ,error)=>{
  setTimeout(succ,1000);
}),new Promise((succ,error)=>{
  setTimeout(succ,2000);
}),new Promise((succ,error)=>{
  setTimeout(error,1000);
})]).then(()=>{
  console.log(1)    
}).catch(()=>{
  console.log(2) // 2
})


Promise.reject(['333333','sadasda']).then((f,x)=>{
  console.log(x)
}).catch((a)=>{
  console.log(a)  // ['333333','sadasda']
})


Promise.resolve(['333333','sadasda']).then((f,x)=>{
  console.log("success") // success
}).catch((a)=>{
  console.log(a)
})
```

* all 要不然就是全胜 要不然就是 失败


#### Demo
```javascript
function ajax(url){
  return new Promise((succ,error) => {
    var ajax = new XMLHttpRequest();
    ajax.open('get',url+'?name='+val.value+'&pass='+mm.value, true);
    ajax.send();
    ajax.onload = function(){
      succ(ajax.responseText);
    };
    ajax.onerror = function(){
      console.log(1)
    };
  }).then((text)=>{
    var json = eval('('+text+')');
    if(json.ok==1){
      alert(json.data)
    } else {
      alert('登录失败')
    }
  }).catch(()=>{
    console.log(1)
  });
}
```

#### webWork
```javascript
var worker = new Worker('li.js');
worker.postMessage("hello");// 发数据
worker.onmessage = function(res){
  console.log(res.data);   // 接收返回的数据
}
// li.js
this.onmessage = function(res){
  var data = res.data;
  var lastDate = data.charAt(0).toUpperCase() + data.substring(1);

  setInterval(()=>{
    this.postMessage(Math.random()); // 定时的返回数据
  }, 1000);
}



//<input id='ipt' >
//<input id='btn' type='button'value ='1111'>
var worker = new Worker('li.js');
btn.onclick = function(){
  worker.postMessage(ipt.value);//发数据
}
worker.onmessage = function(res) {
  console.log(res.data);
}
// li.js
var arr = [];
this.onmessage = function(res) {
  var data = res.data;
  arr.push(data);
  this.postMessage(arr);
}
```

More info:
* [ECMAScript 6 入门](http://es6.ruanyifeng.com/)
* [ES6 常用新特性讲解](https://segmentfault.com/a/1190000010230939#articleHeader0)
* [TypeScript](https://www.typescriptlang.org/docs/home.html)







































