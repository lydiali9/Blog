title: JavaScript
date: 2018-01-29 09:09:01
---
JavaScript is the most widely deployed language in the world. Whether you’re interested in writing front-end client side code, back-end code for servers, or even game development, you’ll be able to accomplish all of this and more with JavaScript.

## 面向对象与原型

### 创建对象
创建一个对象,并且给这个对象新建属性和方法.

``` javascript
var person = new Object();
person.name = 'LynnLi',
person.age = 26;
person.work() {
    return "go to work";
}
alert(person);        // [object Object]
alert(person.name);   // LynnLi
alert(person.work()); // go to work
```

``` javascript
var person = new Object();
person.name = 'LynnLi',
person.age = 26;
person.work() {
    return this.name + " "+ this.age + " go to work";
}
alert(person.work()); // LynnLi 26 go to work
```

* 上面创建了一个对象,并且创建属性和方法,在work()中的this 表示当前作用域下的对象, 也就是表示new Object()实例化出来的那个对象person.
* 这种JavaScript创建对象最基本的方法, 但是有个缺点,如果想要创建类似的对象,就会产生大量的代码, 如下:

例1
```javascript
var person = new Object();
person.name = 'LynnLi',
person.age = 26;
person.work() {
    return this.name + " "+ this.age + " go to work";
}

var person1 = new Object();
person1.name = 'RayJi',
person1.age = 27;
person1.work() {
    return this.name + " "+ this.age + " go to work";
}

alert(person.work()); // LynnLi 26 go to work
alert(person1.work()); // RayJi 27 go to work
```

例2
```javascript
var person = new Object();
person.name = 'LynnLi',
person.age = 26;
person.work() {
    return this.name + " "+ this.age + " go to work";
}

var person1 = person;
person1.name = 'RayJi',
person1.age = 27;
person1.work() {
    return this.name + " "+ this.age + " go to work";
}
alert(person.work()); // RayJi 27 go to work
alert(person1.work()); // RayJi 27 go to work
```

为了解决多个类似对象声明的问题, 可以使用工厂模式的方法,这种方法可以解决实例化对象产生大量重复代码的问题.

### 工厂模式

``` javascript
function createObject(name, age) {
    var obj = new Object();             // 创建对象
    obj.name = name;                    // 添加属性
    obj.age = age;
    obj.work = function() {             // 添加方法
        return this.name + " " + this.age + " go to work";
    }
    return obj;
}

var person = createObject("LynnLi", 26);
var person1 = createObject("RayJi", 27);

alert(person.work());
alert(person1.work());
```

工厂模式解决了重复实例化的问题, 但是还有一个问题, 就是根本无法识别他们是哪个对象的实例. 如下所示:

例1
```javascript
function createObject(name, age) {
    var obj = new Object();             // 创建对象
    obj.name = name;                    // 添加属性
    obj.age = age;
    obj.work = function() {             // 添加方法
        return this.name + " " + this.age + " go to work";
    }
    return obj;
}

var person = createObject("LynnLi", 26);
var person1 = createObject("RayJi", 27);

alert(typeof person);  // object
alert(typeof person1); // object

alert(person instanceof Object);  // true
alert(person1 instanceof Object); // true
```

例2
```javascript
function createObject(name, age) {
    var obj = new Object();             // 创建对象
    obj.name = name;                    // 添加属性
    obj.age = age;
    obj.work = function() {             // 添加方法
        return this.name + " " + this.age + " go to work";
    }
    return obj;
}

function createObject2(name, age) {
    var obj = new Object();             // 创建对象
    obj.name = name;                    // 添加属性
    obj.age = age;
    obj.work = function() {             // 添加方法
        return this.name + " " + this.age + " go to work";
    }
    return obj;
}

var person = createObject("LynnLi", 26);
var person1 = createObject("RayJi", 27);
var person2 = createObject2("RayLi", 28);

alert(person instanceof Object);  // true
alert(person1 instanceof Object); // true
alert(person2 instanceof Object); // true
```

* 不管怎么,他们都是Object类型, 根本无法区别谁是谁的对象.
* 为了解决如上的问题, ECMAScript中可以采用构造函数(构造方法)可用来解决特定的对象.

### 构造函数

``` javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
    this.work = function() {
        return this.name + " " + this.age + " go to work";
    }
}

var person = Person("LynnLi", 26);
var person1 = Person("RayJi", 27);

alert(person.work()); // LynnLi 26 go to work
alert(person1.work()); // RayJi 27 go to work

alert(person instanceof Object);  // true
alert(person1 instanceof Object); // true
```

使用构造函数和使用工厂模式的方法之间的不同:
1. 构造函数方法没有显示的创建对象(new Object());
2. 直接将属性和方法赋值给对象;
3. 没有return语句, 构造函数后台自动返回;

构造函数规范:
1. 构造函数也是函数, 但是函数名第一个字母大写;
2. 必须new 构造函数明
3. 必须使用new运算符

* 使用构造函数的方法, 既解决了重复实例化的问题, 有解决了对象识别的问题. 但是问题是这里并没有new Object(), 为什么可以实例化Person(), 这里原理是什么?

例1
``` javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
    this.work = function() {
        return this.name + " " + this.age + " go to work";
    }
}

function Animal(name, age) {
    this.name = name;
    this.age = age;
    this.run = function() {
        return this.name + " " + this.age + " running";
    }
}

var person = Person("LynnLi", 26);
var person1 = Person("RayJi", 27);
var animal = Animal("Cat", 2);

alert(person instanceof Object);  // true
alert(person1 instanceof Person); // true
alert(animal instanceof Person);  // false 识别了,但是animal是Animal的对象的引用
alert(animal instanceof Animal);  // true 
```

* 对象冒充调用

```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
    this.work = function() {
        return this.name + " " + this.age + " go to work";
    }
}

var obj = new Object();
// alert(obj.work());             // nothing
Person.call(obj, 'LynnLi', 26);   //对象冒充
alert(obj.work());                // LynnLi 26 go to work
```

* 在构造函数中如何使引用类型的值相等
例1
```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
    this.work = function() {  // 实例方法
        return this.name + " " + this.age + " go to work";
    }
}

var person = Person("LynnLi", 26);      // 实例化后地址不同
var person1 = Person("LynnLi", 26);     // 实例化后地址不同
alert(person.name = person1.name);      //true
alert(person.age = person1.age);        //true
alert(person.work() = person1.work());  //true 构造函数体内的方法的值是相等的

alert(person.work);
alert(person1.work);                // 两个打印是相同的

alert(person.work == person1.work); //false 因为他们比较的是引用地址
```

例2
可以把构造函数里的方法用new Function()方法来代替,得到一样的效果,更加证明,他们最终判断的是引用地址,唯一性;

```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
    this.work = new Function("return this.name + ' ' + this.age + ' go to work'");
}

var person = Person("LynnLi", 26);
var person1 = Person("LynnLi", 26);

alert(person.work == person1.work); //false 因为他们比较的是引用地址
```

例3 
我们可以通过构造函数外面绑定同一个函数的方法来保证引用地址的一致性
```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
    this.work = work;
}

function work() {   // 把构造函数内部的方法通过全局来实现引用地址一致
    return this.name + " " + this.age + " go to work";
}

var person = Person("LynnLi", 26);
var person1 = Person("LynnLi", 26);

alert(person.work == person1.work);     // true
alert(person.work() == person1.work()); // true

alert(work()); // NaN go to work
```

* 如上方式虽然解决了保证引用地址一致的问题, 但是这种方式又带来了一种新的问题, 全局中的this在对象调用的时候是Person本身, 而当普通函数调用的时候, this有代表window.

### 原型

我们创建每个函数都有一个prototype(原型)属性, 这个属性是一个对象, 他的用途是包含可以有特定类型的所有实例共享的属性个方法.
1. 原型
```javascript
function Person() {}

Person.prototype.name = "LynnLi";    // 原型属性
Person.prototype.age = 26;
Person.prototype.work = function() { // 原型方法
    return this.name + " " + this.age + " go to work";
}

var person = new Person();
alert(person.name);   // LynnLi
alert(person.work()); //LynnLi 26 go to work
```

2. 构造函数
```javascript
function Person(name, age) {
    this.name = name;         // 实例属性
    this.age = age;
    this.work = function() {  // 实例方法
        return this.name + " " + this.age + " go to work";
    }
}
```

* 如果是实例方法, 不同的实例化, 他们的方法地址是不一样的,是唯一的;
* 如果是原型方法, 那么他们的地址是共享的, 都一样;

```javascript
function Person() {}

Person.prototype.name = "LynnLi";    // 原型属性
Person.prototype.age = 26;
Person.prototype.work = function() { // 原型方法
    return this.name + " " + this.age + " go to work";
}

var person = new Person();
var person1 = new Person();

alert(person.work == person1.work); // true
```

为了进一步了解构造函数的声明方法和原型模式的声明方法, 我们通过图解来了解一下;
![构造函数方式](../images/gouzao.jpg)
![原型模式方式](../images/yuanxing.jpg)





























More info: [Deployment](http://hexo.io/docs/deployment.html)
