---
title: AJAX请求有关知识总结
subtitle: ajax
date: 2015-12-14
categories: AJAX
tags:
    - AJAX
---
## 什么是AJAX?AJAX作用是什么?
async javascript and xml（异步的javascript和xml）
> 作用：实现局部刷新

+ async:我们真实项目中一般使用AJAX从服务器端获取数据都采用异步(当然也可以使用同步)
+ xml:可扩展标记语言,我们一般都会在一个XML文件中,自己扩展一些有特殊含义的标签,通过标签的结构来存储一些数据;一般项目中服务器端返回给客户端的数据都是JSON格式的字符串,但是有一些时候服务器端也会给客户端返回XML格式的字符串，诞生的意义就是用自己定义的标记来存储数据结构的，数据结构比较清晰

AJAX是一门技术,我们在客户端的浏览器中,可以通过AJAX技术向服务器端发送一个请求(根据URL地址发送请求),服务器端根据请求把数据返回,我们使用AJAX中提供的方法或者属性可以获取到服务器端返回的内容

### Request Method (HTTP请求的方式:客户端发送请求的时候设置)
不管用哪一种请求方式,客户端除了发送请求以外也可以给服务器端传递数据,服务器端也可以给客户端返回数据
+ GET 获取,给服务器的少,从服务器拿取的多
+ POST 推送,给服务器的多,从服务器获取的少
+ PUT 增加,在服务器上增加一些资源文件
+ DELETE 删除,在服务器上删除某一个资源文件
+ HEAD 头部,只获取服务器的响应头信息,响应主体内容不获取 ...

### GET vs POST
> GET请求传递给服务器数据一般都是通过URL地址栏问号传参的方式传递给服务器的

> POST请求传递给服务器数据一般都是把传递的数据放在请求

#### [大小限制]
+ GET请求,传递给服务器端的内容存在大小的限制, 因为GET请求传递给服务器的内容都是在URL后面拼接的,如果传递给服务器的内容多,URL就会变的很长,每一个浏览器对于URL的长度都会存在大小的限制(谷歌8KB 火狐7KB IE2KB),超过长度限制的部分会被浏览器截掉
+ POST请求传递给服务器的数据是放在请求主体中的,理论上传递多少内容都可以,没有大小的限制,但是实际工作中,我们一般都会给其做限制,因为如果传递的内容过多,AJAX整体的请求时间会变的很长(一般都在2MB以内最好)
#### [缓存问题]
+ GET请求经常会出现浏览器和服务器之间做一些默认的缓存(这个缓存不是我们所谓的304,304是我们用有特殊的技术去实现出来的,这里的缓存只是浏览器自己的默认一些机制,我们工作中是不需要这个缓存的)
+ POST请求浏览器不会默认为其设置缓存

如果想要清除浏览器默认的缓存,我们需要在URL的末尾追加一个随机数

```javascript
    xhr.open("get","/web/temp.txt?name=gjr&_="+Math.random(),true);

```
#### [安全问题]
+ 由于GET传递给服务器的内容都在URL上,这样话,只要别人把URL劫持了,就可以拦截到你传递给服务器的数据,这样信息就不安全了
+ POST请求是放在请求主体中的,主体中内容是很难被劫持的
### ASYNC

```javascript
    xhr.open([HTTP Method],[URL],[sync/async]);
```
AJAX默认是异步请求的,也就是第三个参数默认是TRUE,如果想使用同步请求,把第三个参数改写为FALSE

### 创建一个AJAX对象
打开一个URL请求地址(发送请求前的一些配置)

#### [请求方式]:
GET系列:GET/DELETE/HEAD

POST系列：POST/PUT
#### [请求地址]：
我们通过这个地址向服务器发送数据请求,请求的地址一般都是后台提供的 (API接口文档)

#### [设置同步异步]：
默认是TRUE异步,设置为FALSE为同步
#### [USER NAME]
#### [USER PASS]：
用户名和密码,某些时候服务器为了保证安全,只允许部分用户能够通过服务器的请求(我们现在一般都是匿名访问),此时需要我们提供安全的秘钥
```javascript
    xhr.open("GET","/userInfo",true)
    var xhr = new xhrHttpRequest;
    xhr.open("GET","URL",true);
    xhr.onreadystatechange=function() {
      if (xhr.readyState === 4 && xhr.status === 200) {
          var result = xhr.responseText;
      }
    }
    xhr.send(null);
```
+ xhr.response：获取响应主体内容(一般不用)
+ xhr.responseText：获取响应主体内容是文本格式(字符串)的
+ xhr.responseXML：获取响应主体内容，而且获取的内容是XML格式(XML文档)的
+ xhr.getResponseHeader([key])：获取响应头信息，例
+ xhr.getResponseHeader("data")就是在获取响应信息中获取服务器的时间
+ xhr.timeout:设置AJAX请求的超时时间，如果当前请求超过这个时间，代表超时，AJAX请求结束，并且会触发ontimeout事件
+ xhr.abort:中断当前的AJAX请求，就是当请求被中断触发的事件
+ xhr.setRequestHeader([key],[value]):设置请求头信息
+ xhr.send(null):发送AJAX请求，AJAX请求这件事从执行send后才开始(之前都是在做准备)，当readyState===4的时候这件事情结束
+ send方法中写的内容就是客户端通过请求主体传递给服务器的内容，不想通过请求主体传递内容则写null

## AJAX的状态
xhr.readyState
#### 0 　　unsent 请求还没有发送(AJAX对象刚刚创建完成)
#### 1　　opened 已经把XHR.OPEN执行完成了，打开，已经完成第二步。
#### 2　　headers_received 响应头信息已经接收,但是主体的内容还没有响应回来
#### 3　　loading 服务器返回的响应主体的内容正在处理加载
#### 4　　done 响应主体内容已经获取成功，被客户端接收到
xhr.status:HTTP状态码(/网络状态码)
通过状态码可以知道当前HTTP事物是否成功，以及失败的原

#### [2开头]:
成功 200-> OK
#### [3开头]:
也代表成功，但是经过了一些特殊处理。
#### Status Code
(HTTP响应状态码:服务器端响应内容的时候设置)
#### xhr.status
#### 200
 OK 正常发送也正常返回,中间没有任何的特殊操作(以2开头的状态码都可以算作成功)
#### 301
Moved Permanently 新版本HTTP协议中，代表 永久转移；之前的版本中代表 永久重定向（一般只应用于域名的跳转）
例如:我们访问http://www.360buy.com/都会重新定向到http://www.jd.com/这 个就是永久转移
#### 302
Move temporarily 新版本的HTTP协议中代表，临时转移了；之前的版本中代表 临时重定向，新版中307代表临时重定向（一般应用于图片的加载等）
例如:服务器的负载均衡,对于很多大公司的项目,网站中的资源图片很多都是采 用的302做的临时转移,以此解决主服务器的压力
#### 304
Not Modified 读取的是缓存数据(重要，网站优化的一些特别重要的手段我们一般 会把静态资源文件css/js/img做304缓存)
例如:对于不经常更新的JS/CSS/IMG等,第一次请求完成后我们做一个缓存,以后 在请求我们直接读取缓存中的内容,这个技巧也是前端开发中常规的优化手段
#### 400
Bad Request
#### 401
Unauthorized 传递给服务器的参数出现错误
#### 403
Forbidden 无权限访问,接收到请求了，但是没有返回正常的结果，但是就是 不告诉你错误原因，有些时候会返回404
#### 404
Not Found 找不到,请求的URL地址是错误的
#### 413
Request Entity Too Large 客户端传递给服务器的内容超过了服务器愿意 接收的范围
#### 500
Internal Server Error 未知的服务器错误,服务器可能宕机了也可能是其它的 原因,总之是服务器的错误
#### 503
Service Unavailable 服务器压力过大超负荷,比如一台服务器只能同时处理1000 个访问，那么从第1001个开始都是503,一般遇到503多刷新几次可能就好了
> 说到AJAX就会不可避免的面临两个问题，第一个是AJAX以何种格式来交换数据？第二个是跨域的需求如何解决？这两个问题目前都有不同的解决方案，比如数据可以用自定义字符串或者用XML来描述，跨域可以通过服务器端代理来解决。 但到目前为止最被推崇或者说首选的方案还是用JSON来传数据，靠JSONP来跨域。而这就是本文将要讲述的内容。

## AJAX配置

### 打开一个URL请求地址(发送请求前的一些配置)
+ [请求方式]:GET系列:GET/DELETE/HEAD,POST系列:POST/PUT
+ [请求地址]:我们通过这个地址向服务器发送数据请求,请求的地址一般都是后台提供的(API接口文档)
+ [设置同步异步]:默认是TRUE异步,设置为FALSE为同步
+ [USER NAME]:用户名
+ [USER PASS]:密码,某些时候服务器为了保证安全,只允许部分用户能够通过服务器的请求(我们现在一般都是匿名访问),此时需要我们提供安全的秘钥 xhr.open("GET","/userInfo",true); 

### 监听AJAX状态的改变,实现不同的业务操作
+ xhr.readystate:AJAX状态
    + 0:UNSEND:未发送,刚开始创建完成AJAX实例,初始状态就是0
    + 1:OPENED:已打开,就是已经执行完成第二步操作了
    + 2:HEADERS_RECEIVED:客户端已经接收到服务器返回的响应头信息
    + 3.LOADING:服务器返回的响应主体内容正在传输中
    + 4.DONE:响应主体内容已经被客户端接收
+ xhr.status:HTTP状态码(同上述ajax状态码)

+ 操作
+ xhr.response:获取响应主体(一般不用)
+ xhr.responseText:获取响应主体内容,而且获取的内容是文本格式(字符串)的:
+ xhr.responseXML:获取响应主体内容,而且获取的内容是XML格式(XML文档)的
+ xhr.getResponseHeader([key]):获取响应头信息,key为关键字,如Date可获取扶服务器的时间
+ xhr.getAllResponseHeader:获取全部的响应头信息
+ xhr.timeout:设置AJAX请求的超时时间,如果当前请求超过这个时间,代表超时,AJAX请求结束,并且会触发ontimeout事件
+ xhr.abort():中断当前AJAX请求,可以触发onabort事件
+ xhr.setRequsetHeader([key],[value]):设置请求头信息
    + 设置请求头之前必须保证已经执行open了
    + 设置请求头的内容不能是中文(除非进行编码)

## AJAX请求发送
AJAX请求这件事从执行send后才开始(之前都是在做准备),当readystate=4的时候这件事结束

xhr.send(null):send方法中写的内容就是客户端通过请求主体传递给服务器的内容,不想通过请求主体传递内容我们写null

## js编码和解码
如果就是想通过请求头给服务器传递中文汉字,我们需要先把传递的内容进行编码,由服务器进行编码

+ escape/unescape:这种方式经常应用于客户端对中文汉字和特殊字符进行编码的(一般不常用,因为服务器端语言,除了NODE其他的貌似不支持这个方法)
+ encodeURI/decodeURI:按照UNICODE编码解码
+ encodeURIComponent/decodeURIComponent:相对于上面的方法来说可以把特殊字符也进行编码,而encodeURI只能编码中文汉字
    + 服务器端接受的时候再进行解码decodeURIComponent
    
## 加密
加密不是简单的编码解码,而是按照规则进行加密解密(项目中的重要信息我们一般都要进行加密的)

+ [可逆转的加密]
    + 重要的信息加密后还可以再解密回来,例如:电话号码或者银行卡号进行加密
    + 一般公司都是自己写加密和解密规则
+ [不可逆转的加密]
    + 加密后就不可解密了,例如:所有密码都是不可逆转的加密
    + 最常用的是md5,sha1....
    + 真正项目中会把MD5加密后的结果进行二次或者多次处理

## GET/POST系列请求
> 不管哪种HTTP请求方式,客户端都可以把信息传递给服务器,服务器也可以把内容返回给客户端

+ GET系列
    + GET:获取,给服务器的少,从服务器拿的多
    + DELETE:删除,从服务器上删除一些资源文件
    + HEAD:获取响应头,使用该请求只能获取服务器的响应头信息,获取不到响应主体内容

+ POST系列    
    + POST:推送,给服务器的多,从服务器拿的少
    + PUT:放,在服务器上放入一些资源文件

+ GET系列和POST系列存在一些主要的区别
    + GET请求传递给服务器内容通过的是"问号传参",POST系列传递给服务器内容通过的是"请求主体"
    + 每个浏览器对于URL的长度都是一个最大限制(谷歌8kb/火狐7kb/IE2kb),如果POST请求也用问号传参,很有可能导致URL的长度超出限制(POST传递的内容多),超出的部分会被浏览器自动截取掉,导致信息不完整,所以POST应该使用请求主体,GET可以使用问号传参(GET传递的少);请求主体传递的内容大小理论上没有限制,真实项目中为了保证上传的速度,我们一般自己会限制其大小(例如:上传文件要求限制在100KB以内)
    ```javascript
          [GET]
          xhr.open("GET","/userInfo?name=zxt&age=27&sex=1");
          xhr.send(null);
          [POST]
          xhr.open("POST","/userInfo");
          xhr.send("name=zxt&age=27&sex=1");
    ```
    + GET系列请求会出现不可控制的缓存,而POST请求不会
        + 原因是因为GET是通过问号传参的方式传递给服务器的,如果我发送两次请求,地址和传递的参数都一摸一样,会触发浏览器的记忆识别功能,它认为你第二次请求的数据和第一次一样,所以会把缓存中的数据返回;有些需求这样不好,例如随时获取股票信息等需求此时我们需要清除GET请求的缓存;
        + [清除缓存只需要在每一次请求的地址后面加一个随机数,保证每一次请求的地址不完全一致即可;属性名使用_是为了不和其他有意义的属性名冲突(行业规范)]
        ```javascript
              xhr.open("GET","/userInfo?_="+Math.random());
              xhr.send(null);
        ```
    + GET系列请求相对于POST请求来说不安全,在互联网面前没有绝对的安全,我们平时上网都是在裸奔
        + 原因:GET传递个服务器的内容都在URL上(问号传参),有一种比较简单的黑客技术"URL劫持",别人可以通过劫持URL把我们传递给服务器的数据捕获到
        + 以后只要给服务器传递的是重要的信息,不管内容多少都应该使用POST
            
## 获取服务器时间
我们获取的服务器时间和当前真实的时间,出现一些差距"时间差",我们获取的时候是服务器返回数据的时间,客户端接收到数据也需要一点时间,所以当客户端接收到这个值的时候,已经距离真实的时间差了一点
+ 减少时间差
    + 在xhr.readyState==2这个阶段获取服务器时间(AJAX请求必须是异步的,同步的只能在状态为4的时候处理)
    + xhr.open("HEAD"...)请求方式是HEAD请求,我们只需要获取响应头即可
    + head请求会产生缓存问题,使用Math.random();
    + 只是用一次AJAX请求,在获取的时间中进行递增,来同步服务器的时间,减少服务器的压力

## jQ中的AJAX
```javascript
$.ajax("note.txt",{
    method:"get",
    dataType:"txt",
    async:true,
    cache:false,
    data:{
        name:"zxt",
        age:"[27,28]"
    }
    success:function(){},
    error:function(){},
    complete:function(){}
})
```
+ method:等同于type属性
+ dataType:txt(默认),预先设定服务器返回数据的格式,不管我们设置什么值,服务器返回给我们的数据一般都是字符串或者XML格式的,组件库的目的就是根据我们设定的值把服务器返回的结果转换成我们所需要的->"dataType"的值并不能影响服务器的返回结果
+ async:设置同步还是异步,默认是true,异步
+ cache:如果当期的请求是GET系列,我们设置的还是false,此时需要我们去清除GET请求的缓存,默认值true
+ data
    + 所有需要传递给服务器的内容都放在DATA中,GET系列采用问号传参,POST系列采用请求主体;DATA中的字符串内容是什么我们传递的就是什么
    + 还可以是个对象,POST系列会把这个对象直接放在请求主体中处理,DATA中是什么放入的就是什么;GET系列会把它拆分XXX=XXX这种形式,放在URL的末尾以问号传参传递给服务器,值得注意的是,对于汉字和特殊字符,我们需要把内容进行编码
+ complete:不管成功还是失败,只要完成就会执行这个

## 模拟jQ中AJAX
+ 思想
    + 传入为一个对象,避免顺序问题导致参数混乱
    + 对传入的参数进行默认操作
        + 使用method进行默认,不使用Type
    + 将传入的对象将默认值进行覆盖,没传递的走默认值,传递的走自己需要的值
        + 将Type和method进行统一,最后使用method
    + 发送AJAX请求
        + 对XMLHttpRequest进行兼容处理
        + 在onreadystatechange事件中,分情况进行讨论        
            + 请求失败
            + 请求成功
                + 对readystate的情况进行讨论
                + 获取响应主体内容(一般是字符串),根据dataType值来处理响应主体内容,处理失败,结果为空,避免报错
                + 讨论对应的回调函数并传入响应主体执行函数 
                    + 回调函数中的this为xhr对象
            + 根据method对data参数进行处理,并传入send()中
    + 根据dataType和cache的值来进行缓存清除操作
        + 验证url是否?是否存在,来对url进行随机数的添加                

