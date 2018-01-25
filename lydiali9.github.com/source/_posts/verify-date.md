title: 验证时间
date: 2016-01-01 08:49:23
tags: ['strtotime', 'time', 'date']
---

### 验证时间
> 网络上现在流传着各种有关验证时间的正则表达式；但是每当去验证这些正则表达式时，总是会遇到各种问题，如下是验证时间的正确方法。

满足条件：
1. 判断是不是日期(时间)格式；
2. 天数符合每月天数的时间范围；
3. 2月天数符合平年、闰年的计算；

##### 验证时间格式为'Y-m-d'
```
$ $unixTime = strtotime($time); // 将日期时间解析为 Unix 时间戳
  if ((date("Y-m-d", $unixTime) == $time) === false) { // 格式化一个本地时间／日期
     echo "Failed to verify the time:" . $time;
  }
```