title: Event Delegation
date: 2018-02-05 09:09:01
tags: ['event delegation']
categories: ['event delegation']
---

## 模拟操作

点击按钮事件
``` javascript
$(function() {
    $('input').click(function() {});
})
```

Example 1 (模拟用户点击操作)
``` javascript
$(function() {
    $('input').click(function() {});
    $('input').trigger('click');
})
```

Example 2
``` javascript
$(function() {
    $('input').click(function() {}).trigger('click');
})
```

Example 3 (传参)
``` javascript
$(function() {
    $('input').click(function(e, data1, data2) {
        alert(data1 + data2);             // 123abc
    }).trigger('click', ['123', 'abc', ['a', 'b'], {username: 'Lynn'}]);  // trigger传入多个参数, 只有一条的时候可以省略中括号, 多条之后就无法识别
})
```

自定义事件
``` javascript
$(function() {
    $('input').bind("myEvent", function() {
        alert('custom event');
    }).trigger('myEvent');
});
```

trigger简写
``` javascript
$(function() {
    $('input').click(function() {
        alert('event');
    }).click();
});
```

triggerHandler
``` javascript
$(function() {
    $('input').click(function() {
        alert('event');
    }).triggerHandler('click');
});
```

> trigger和triggerHandler之间的区别
1. triggerHandler方法并不会触发事件的默认行为, 而trigger会.
2. triggerHandler方法只会影响第一个匹配到的元素, 而trigger会影响所有
3. triggerHandler方法会返回当前事件执行的返回值, 如果没有返回值,就返回undefined; 而trigger返回当前包含事件触发元素的Jquery对象.
4. trigger在创建事件的时候,会冒泡; triggerHandler方法不会冒泡.

## 命名空间
有时候, 我们想要对事件进行移除. 但是同名同元素绑定的事件移除比较麻烦, 这个时候我们可以使用命名空间来解决;

```javascript
$('input').bind('click.abc', function() {
    alert('abc')
});

$('input').bind('click.xyz', function() {
    alert('xyz')
});

$('input').unbind('click.abc'); // 移出click事件中命名空间为abc的
```

## 事件委托
在JQuery中, 我们通过事件冒泡的特性, 让自元素绑定的事件冒泡到父元素上,然后再进行相关的处理即可;

#### live()和die()
```javascript
<div>
    <input type="button" class="btn-class" value="button" />
    <input type="button" class="btn-class" value="button" />
    <input type="button" class="btn-class" value="button" />
</div>
```

.bind绑定了三个事件
``` javascript
$(".button").bind("click", function() {
    alert("事件没有委托");
});
```

使用live, 必须引用向下兼容第三方插件, jquery-migrate-1.2.1.js
使用live绑定的是document, 而非button. 所以,永远只会绑定一次事件
``` javascript
$(".button").live("click", function() {
    alert("事件委托");
});
```

.bind无法动态绑定事件
``` javascript
$(".button").bind("click", function() {
    $(this).clone().appendTo('#box');
});
```

而live可以动态绑定事件, 因为事件绑定在document上
``` javascript
$(".button").live("click", function() {
    $(this).clone().appendTo('#box');
});

$(".button").die("click"); // same as unbind()
```

> live绑定在document上, 而点击.button其实是冒泡到document上, 并且点击document的时候, 需要验证event.type和event.target才能触发;
> live()无法使用链接连缀调用, 因为参数的特性导致的;
> 当我们需要停止事件委托的时候, 可以使用.die()来取消掉;
> 由于.live() 和.die()在JQuery1.4.3版本中废弃了, 之后推出语义清晰,减少冒泡传播层次,又支持链接连缀调用方式的方法: delegate()和.undelegate()

#### delegate()和.undelegate()
```javascript
$("#box").delegate('.button', 'click', function() {
    alert(123);
});
```
live语义不清晰, 由于他没有指定绑定了谁
delegate语义清晰, 绑定谁, 谁就在开头
``` javascript
$("#box").undelegate('.button', 'click');
```
支持连缀调用方式
``` javascript
$('div').first().delegate(".button", "click", function() {
    $(this).clone().appendTo('div:first');
});
```

> delegate()需要制定父元素, 然后第一个参数是当前元素, 第二个参数是事件方式, 第三个参数是执行函数. 和bing()方法一样, 可以传递额外参数; undalegate()和dalegate()方法一样可以直接删除所有事件, 比如: .undelegate("click"). 也可以删除命名空间的事件, 比如: undelegate('click.abc');
> live()和delegate()和bind()方法一样都是事件绑定, 那么区别也很明显, 用途上遵循两个规则:
1. 在DOM中很多元素绑定相同事件时;
2. 在DOM中尚不存在即将生成的元素绑定事件时;
我们推荐事件委托的绑定方式, 否则使用.bind()的普通绑定;

> 最终这个方法在jQuery1.7版本中被.on()方法整合代替了

#### on,off和one

##### 普通绑定.bind .unbind

替代bind
``` javascript
$(".button").on("click", function() {
    alert("替代bind");
});
```

替代bind()的方式, 并使用额外的数据和事件对象
``` javascript
$(".button").on("click",  {user: "Lynn"}, function(e) {
    alert("替代bind" + e.data.user);
});
```

替代.bind()方式, 并绑定多个事件
``` javascript
$(".button").on("mouseover mouseout", function() {
    alert("替代.bind()移入移出");
});
```

替代bind()方式, 以对象模式绑定多个事件
``` javascript
$(".button").on({
    mouseover: function() {
        alert("替代.bind()移入");   
    },
    mouseout: function() {
        alert("替代.bind()移出");   
    },
});
```

替代bind()方式, 阻止默认行为并取消冒泡
``` javascript
$("form").on("submit", function() {
    return false;
});

$("form").on("submit", false);
```

替代bind()方式, 阻止默认行为
``` javascript
$("form").on("submit", function(e) {
    e.preventDefault();
});
```

替代bind()方式, 取消冒泡
``` javascript
$("form").on("submit", function(e) {
    e.stopPropagation();
});

$("form").off("submit");
```

##### 事件委托.live .delegate 解除委托.die .undelegate

替代.live .delegate
``` javascript
$("#box").on("click", ".button", function() {
    $(this).clone().appendTo("#box");
});
```

移出事件委托
``` javascript
$("#box").off("click", ".button");
```

不管是.bind()还是.on()绑定事件后都不是自动移除事件的, 需要通过unbind()和off()来手工移除. jQuery提供了.one()方法, 绑定元素执行完毕后自动移除事件, 可以方法仅仅触发一次

#### one()

仅一次事件触发
```javascript
$(".button").one("click", function() {
    alert("仅一次事件触发");
});
```

事件委托方式绑定, 仅仅一次事件触发
```javascript
$(".button").one("click", ".button", function() {
    $(this).clone().appendTo("#box");
});
```