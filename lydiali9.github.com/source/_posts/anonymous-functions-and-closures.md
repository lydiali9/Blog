title: Anonymous functions and closures
date: 2017-09-01 09:09:01
---

匿名函数就是没有名字的函数, 闭包是可以访问一个函数作用域里变量的函数.

## 匿名函数

普通函数
```javascript
function box() {
    return "Lynn";
}
```

匿名函数
```javascript
function() {
    return "Lynn"; // 单独的匿名函数是无法运行的, 会报错
}
```

把匿名函数赋值给变量
```javascript
var box = function() {
    return "Lynn";
}

alert(box());
```

通过自我执行来执行匿名函数
```javascript
(function() {
    return "Lynn";  
})();
```

把匿名函数自我执行的返回值赋给变量
```javascript
var box = (function() {
    alert("Lynn");
})();
alert(box);
```

自我执行匿名函数的传参
```javascript
(function(age) {
    alert(age);
})(100);
```

函数里放一个匿名函数
```javascript
function box() {
    return function() {  // 闭包
        return "Lynn";  
    }
}
alert(box()());
```

## 闭包
闭包是指有权访问另一个函数作用域中的变量的函数, 创建闭包的创建方式, 就是在一个函数内部创建另一个函数, 通过另一个函数访问这个函数的局部变量

通过闭包可以返回局部变量
```javascript
function box() {
    var user = "lynn";
    return function() {
        return user;
    };
}
alert(box()());

var b = box();
alert(b());
```

> 使用闭包有个优点, 也是他的缺点: 就是可以把局部变量驻留在内存中, 可以避免使用全局变量.

使用全局变量进行累加
```javascript
var age = 100;
function box() {
    age++;
}
alert(age); // 101
box();
alert(age); // 102
```

使用局部变量进行累加
```javascript
function box() {
    var age = 100;
    age++;
    return age;
}
alert(box()); // 101
alert(box()); // 101
```

使用匿名函数实现局部变量驻留内存中从而实现累加
```javascript
function box() {
    var age = 100;
    return function() {
        age++;
        return age;
    };
}
alert(box()()); // 101
alert(box()()); // 101


var b = box();
alert(b());    // 101
alert(b());    // 102
// 用完之后手动销毁 
b = null;
```

> 由于闭包里作用域返回的局部变量资源不会被立刻销毁回收, 所以可能会占用更多的内存. 过度使用闭包会导致性能下降, 建议在非常有必要的时候使用闭包; 

> 作用域链的机制导致一个问题, 在循环中的匿名函数取得的任何变量都是最后一个值.

循环里包含匿名函数的取值问题
```javascript
function box() {
    var arr = [];
    for(var i = 0; i < 5; i++) {
        arr[i] = function() {
            return i;  // arr[0] = 0, arr[1] = 1 ... arr[4] = 4     
        };
    }
    // 循环已经执行完毕了, 最终是4++ = 5, 那么最终就是5
    return arr;
}
alert(box()); // function() {return i;}, .... function() {return i;}
alert(box()[0]); // function() {return i;}

var b = box();
for(var j = 0; j < 5; j++) {
    alert(b[i]);     // 输出5个: function() {return i;}
}

var b = box();
for(var j = 0; j < 5; j++) {
    alert(b[i]());     // 输出5个5
}
```

版本1
```javascript
function box() {
    var arr = [];
    for(var i = 0; i < 5; i++) {
        arr[i] = i;
    }
    return arr;
}

var b = box();
alert(b);  // 0 1 2 3 4

for(var j = 0; j < 5; j++) {
    alert(b[i]);     // 0 1 2 3 4
}
```

版本2
```javascript
function box() {
    var arr = [];
    for(var i = 0; i < 5; i++) {
        arr[i] = (function(num) {  // 通过自我及时执行匿名函数
            return num;     
        })(i);
    }
    return arr;
}

var b = box();
for(var j = 0; j < 5; j++) {
    alert(b[i]);     // 0 1 2 3 4
}
```

版本3
```javascript
function box() {
    var arr = [];
    for(var i = 0; i < 5; i++) {
        arr[i] = (function(num) {  
            return function() {     // 因为闭包可以将变量驻留在内存中, 和如上累加是一样的
            return num;     
        };     
        })(i);
    }
// 已经执行完毕了, num为什么可以0 1 2 3 4
    return arr;
}

var b = box();
for(var j = 0; j < 5; j++) {
    alert(b[i]);     // 输出5个function() {return num;}
}

for(var j = 0; j < 5; j++) {
    alert(b[i]());     // 0 1 2 3 4
}
```

版本4
```javascript
function box() {
    var arr = [];
    for(var i = 0; i < 5; i++) {
        arr[i] = function(num) {  
            return function() {     // 因为闭包可以将变量驻留在内存中, 和如上累加是一样的
            return num;     
        };     
        }(i);
    }
// 已经执行完毕了, num为什么可以0 1 2 3 4
    return arr;
}

var b = box();
for(var j = 0; j < 5; j++) {
    alert(b[i]());     // 0 1 2 3 4
}
```

## 关于this对象

在闭包中使用this对象也可能导致一些问题; this对象是在运行时基于函数的循环环境绑定的, 如果this在全局范围就是windows,如果在对象内部就指向这个对象. 而闭包却在运行时指向window的, 因为闭包并不属于这个对象的属性和方法;

Example 1
```javascript
var box = {
    getThis: function() {
        return this;
    }
};
alert(this);          // [object Window]
alert(box.getThis()); // [object Object]
```

Example 2
```javascript
var box = {
    getThis: function() {
        return function() {   // 闭包不属于box, 里面的this指向window
            return this;
        };
    }
};
alert(box.getThis());   // function() { return this;}
alert(box.getThis()()); // [object Window]
```

Example 3
```javascript
var user = 'The Window';

var box = {
    user: 'The Box',
    getUser: function() {
        return this.user;
    }
};

alert(user);           // The Window
alert(box.getUser()()); // The Box
```

Example 4
```javascript
var user = 'The Window';

var box = {
    user: 'The Box',
    getUser: function() {
        return function() {
            return this.user;
        };
    }
};

alert(box.getUser());   // function() {return this.user;}
alert(box.getUser()()); // The Window
```

Example 5(对象冒充)
```javascript
var user = 'The Window';

var box = {
    user: 'The Box',
    getUser: function() {
        return function() {
            return this.user;
        };
    }
};
alert(box.getUser().call(box)); // The Box
```

Example 6
```javascript
var user = 'The Window';

var box = {
    user: 'The Box',
    getUser: function() {
    // 这里作用域的this是Box
    var that = this;
        return function() {
        // 这里作用域的this是window
            return that.user;
        };
    }
};
alert(box.getUser()()); // The Box
```

## 内存泄漏
```javascript
obj = null; // 手工解除引用, 等待垃圾回收
```

## 模仿块级作用域

块级作用域(私有作用域)
```javascript
function box() {
    for(var i = 0; i < 5; i++) {
        
    }
    alert(i);
}
box(); // 5
```

Example 1
```javascript
function box() {
    for(var i = 0; i < 5; i++) {
        
    }
    var i;  // 及时重新声明, 也不会影响之前声明初始化的数据
    alert(i);
}
box(); // 5
```

> JavaScript没有块级作用域

使用块级作用域
```javascript
function box() {
    (function() {  // 包含自我执行的匿名函数, 就可以实现私有作用域
        for(var i = 0; i < 5; i++) {
            alert(i); // 0 1 2 3 4
        } 
    })();         // 出了这个私有作用域, 变量立即被销毁
    alert(i);     // 这里就不能识别了
}
```





























