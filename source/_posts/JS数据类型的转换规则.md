---
title: JS数据类型的转换规则
subtitle: cutType
date: 2017-11-13
categories: JS
tags:
    - 数据类型
---
## 1 如果只有一个值,判断这个值是真还是假,遵循只有0,NaN,'',null,undefined这五个是假的,其余的都是真

## 2 如果是两个值比较是否相等,遵循以下规则:
+ ![]->false
+ []==[]->false
+ //val==val 两个值可能不是同一个数据类型的,如果是两个等于号比较的话,会默认的进行数据类型转换
+ 规则:
    + 1 对象==对象 永远不相等
    + 2 对象==字符串
        + 先将对象转换成字符串(调用toString的方法),然后再进行比较
            + []空数组转化为字符串是''
            + {}转化为字符串"[object,objec]"
    + 3 对象==布尔类型
        + 对象先转化字符串(toString方法),然后再把字符串转化为数字(Number,""空字符串为0) ,布尔类型也转化为数字(true是1,false是0),最后让两个数字比较[]==false->true
    + 4 对象==数字
        + 先将字符串转化为字符串(toString), 然后把字符串再转化为数字(Number),然后再进行比较
    + 5 数字==布尔
        + 将布尔转化为数字
    + 6 数字==字符串
        + 字符串转化为数字
    + 7 字符串==布尔
        + 都转化为数字
    + 8 null==undefined 结果为true;
    + 9 null或者undefined 和其他任何的数据类型比较都不相等

## 3 除了==是比较,===也是比较(绝对比较)
val === val2 比较的是数据类型,如果数据类型不一样肯定不相等

<hr/>

```javascript
    // 两个值之间的比较：
    [] === false ->true;
    [] === false ->false;
    ![] === false ->true;
```
两个值进行比较,如果数据类型不一样的话,会进行默认的数据类型转换

->对象 == 对象：比较的是各自代表的内存地址,地址相同则相等,地址不同则不相等

->对象 == 数字：先把对象toString,然后把结果再Number,最后和数字比较

->对象 == 字符串：先把对象toString,然后再比较

->对象 == 布尔：都转换成数字
```javascript
    [] === false => [].toString() ->""->Number("")->0
                    => false -> 0  true -> 1
                    => 0 === 0 => true
```
->字符串 == 数字：字符串转换成数字

->字符串 == 布尔：都转换成数字

->布尔 == 数字 ：布尔转换成数字

->null == undefined是相等的,它们和自身也是相等的,除此之外和其他任何值都不想等

->NaN == NaN是不相等的

把一个值转化成布尔类型：!、!!、Boolean、if(num)条件的意思也是先把num转换成布尔值验证真假

->只有''、0、NaN、null、undefined代表false,其余的都是true

![] == false:两个==判断的优先级是最低的,它肯定是先把左右两边的表达式执行,执行的结果再进行比较

![]转换成布尔再取反 ->false =>true

