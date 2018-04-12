title: nginx 502 502 Bad Gateway
date: 2017-07-01 12:09:01
tags: ['nginx', '502 Bad Gateway']
categories: ['nginx', '502 Bad Gateway']
---

### Cause
1. 在会员管理页面， 通过勾选筛选条件筛选会员，筛选失败；


### Analysis
1. 筛选失败，服务器返回 ``502 Bad Gateway``；
   当筛选条件的相应头携带少量的信息时，搜索会员成功；
2. 此bug在本地一直无法重现；stage和dev都可以重现；
3. 查看nginx的error.log日志文件，报错``upstream sent too big header while reading response header from upstream``；
4. 反馈回来的头部信息太大，浏览器无法做处理，而且不同的浏览器也会有不同，可能还回收浏览器自身所安装的插件的影响；

### Solution Analysis 

#### 方案一：

1. 首先在nginx配置文件添加如下配置：
```php
proxy_buffer_size   512k;
proxy_buffers   4 512k;
proxy_busy_buffers_size   512k;
```


2. 如果依然包这个错误，还有一种可能就是``没有重启PHP5程序服务``,继续修改配置问题如下：
在nginx配置文件http{}中添加如下的配置：
```php
http {
  proxy_buffer_size   128k;
  proxy_buffers   4 256k;
  proxy_busy_buffers_size   256k;
}
```


然后在location ~ .php$ {}中添加如下的配置：
```php
fastcgi_buffer_size 128k;
fastcgi_buffers 4 256k;
fastcgi_busy_buffers_size 256k;
```
最后，重新启动ngxin,和php5服务；

#### 方案二：

1. 减小cookie size，也就是我们在发出post请求的时候，将参数不要携带在url里面，传入body中；

2. 将nginx的配置设置最好改为原始的配置数据；
```php
http {
  fastcgi_buffers 8 4k;
  fastcgi_buffer_size 4k;
}
```

### Solution
1. 通过修改nginx，或者php5的配置的参数，这种方案可能会解决当前的问题，但是从实际上并没有解决问题；随着系统越来越庞大，数据量可能会越来越多，url的携带的头部信息也会越来越多，这样依旧会出现502的报错；
2. 不同的浏览器处理cookies size的大小也不同，可能还受本身浏览器插件影响，所以问题依然存在；
3. 最后的解决方法应该是修改api的方法，这种参数的请求通过body传参数，而不是url携带参数；
