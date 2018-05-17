---
title: devicePixelRatio
date: 2018-05-17 11:19:56
tags: ['devicePixelRatio', 'viewport']
categories: ['devicePixelRatio']
---

最近正在做移动端的开发， 想要让我们的网页更好的去响应或者适配在不同分辨率的移动设备，至此我也去各种查找资料并且尝试各种情景做了一系列的学习，总结如下：

## 视口
> Viewport，想必每一位朋友在做移动端的开发时，都会见过这个单词，它主要包括三个方面： 视觉视口，理想视口和布局视口.

### 布局视口

#### 概念
布局视口跟视觉视口不一样，它不是指设备屏幕区域，它是为了解决PC端网站在移动端显示不佳的一个解决方案。布局视口通常比设备屏幕宽得多，一般为980px，但也不是唯一，在不同的浏览器中也会有所不同。
因此在手机上，视口与移动端浏览器屏幕宽度不再相关联，是完全独立的，这个浏览器厂商定的视口被称为布局视口。

![布局视口](https://github.com/lydiali9/Blog/blob/master/lydiali9.github.com/images/layoutViewPort.png?raw=true)

布局视口我们是看不见的，只知道网页的最大宽度是980px，并且被缩放在了屏幕内。
可以这样设置布局视口的宽度：

```bash
< meta  name = " viewport "  content = " width=640 " >
```

#### 媒体查询与布局视口
700px 指的是布局视口的宽度

```bash
@media ( min-width : 700 px ){
    ...
}
```

> 我们可以通过document.documentElement.clientWidth | document.documentElement.clientWidth来获得布局视口的宽和高。

> 注意：布局视口的宽高值是在页面没添加viewport时所获得的值。如果你给页面添加了viewport并且设置了width = device-width时，通过上面的代码所获得的值就不是布局视口的默认值了。

### 视觉视口

视觉视口是用户正在看到的网页的区域，大小是屏幕中CSS像素的数量。

![视觉视口](https://github.com/lydiali9/Blog/blob/master/lydiali9.github.com/images/visualViewPort.png?raw=true)

window.innerWidth/Height返回视觉视口的尺寸

### 理想视口

布局视口明显对用户是不友好的，完全忽略了手机本身的尺寸。这个理想视口是为了布局视口而生的，为什么这么说，因为它是基于布局视口的。它其实就是变了尺寸的布局视口。理想视口就是把布局视口调整到合适的状态，让页面有最好的表面效果，这也是它名字的由来。设置了理想视口用户就不再需要对页面进行缩放，因为浏览器已经帮你把页面调整到最佳的显示状态了。

当然了，如果你的网站是PC端的或者没有作响应式处理的。虽然布局视口已经调整到最佳显示状态，但你所看到的网页还不是你想要的，因为整个PC页面都压缩到理想视口大小了，内容基本都看不清。所在理想视口是会对于移动页面的，这个要在移动设备上才能看到效果，如果在PC上通过浏览器的设备模式查看的话页面是不会挤在一起的，你能看到的是页面的一个部分，你可以通过滚动来查看页面中的不同区域. 

可以这样设置告诉浏览器使用它的理想视口：

```bash
<meta name="viewport" content="width=device-width">
```

定义理想视口是浏览器的事情，并不能简单地认为是开发者定义的，开发者只能使用。

### Viewport属性

> width：控制viewport的大小，你可以给它指定一个值，如：600，或者甚至还可以给它一个特殊的值，如：device-width，device-width为设备的宽度（单位为缩放为100%时的CSS的像素）。
height：与width相对应，指定viewport高度。
initial-scale：初始缩放比例，也即是当页面第一次load的时候缩放比例。
maximum-scale：允许用户缩放到的最大比例。
minimum-scale：允许用户缩放到的最小比例。
user-scalable：是否允许用户手动缩放。


## 像素

在移动端给一个元素设置width:9px时发生了什么？这里的px到底是多长呢？像素是网页布局的基础，但是我们一直在用直觉使用它。

### 物理像素(physical pixel)
我们看到的每个屏幕都是由一颗颗我们肉眼难以看到的小颗粒（物理像素）组成的。

![物理像素](https://github.com/lydiali9/Blog/blob/master/lydiali9.github.com/images/devicePixels.png?raw=true)

### 逻辑像素

是计算机坐标系统中的一个点，这个点代表一个可以由程序使用的虚拟像素(比如说CSS像素)。

### 设备的像素比(device pixel ratio)简称DPR

它的数值体现了物理像素和逻辑像素之间的关系，用公式可以计算出该设备的DPR的大小：

```bash
DPR = 物理像素 / 逻辑像素
```

> 那么了解了上面这些概念，就可以知道，为什么css在pc上写着font-size=12px;但是换到手机上却变小了？因为DPR.我们在电脑屏幕上的DPR是1，但是手机却不同，可能是它可能是2，也可能是3。然而我们可以通过获取设备DPR的方法对不同DPR的设备，做一些样式适配.

## 缩放

### 缩放与设备像素、CSS像素的关系

缩放是在放大或缩小CSS像素，比如一个宽度为200px 的元素无论放大，还是200个CSS像素。但是因为这些像素被放大了，所以CSS像素也就跨越了更多的设备像素。缩小则相反。

### 缩放与视口

#### 缩放会影响视觉视口的尺寸

页面被用户放大，视觉视口内CSS像素数量减少；被用户缩小，视觉视口内CSS像素数量增多就行了。这个道理应该是不难想的。

#### 用户缩放不会影响布局视口

注意，这是『用户缩放』，后面会说开发者设置缩放的情况

### 缩放比例

我们在开发者工具中可以在这里查看缩放比例.

### 禁止缩放

```bash
<meta name="viewport" content="user-scalable=no">
```

### 设置缩放

```bash
<meta name="viewport" content="initial-scale=2">
```

> 使用initial-scale有一个副作用：同时也会将布局视口的尺寸设置为缩放后的尺寸。所以initial-scale=1与width=device-width的效果是一样的。


## 完美适配

```bash
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no, minimal-ui"> 
```

More info: [移动web适配利器-rem](http://www.alloyteam.com/2016/03/mobile-web-adaptation-tool-rem)
[screensiz](http://screensiz.es/phone)
[devicepixelrati](https://www.quirksmode.org/blog/archives/2012/06/devicepixelrati.html)