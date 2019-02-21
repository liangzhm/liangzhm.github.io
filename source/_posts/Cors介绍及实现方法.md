---
title: Cors介绍及实现方法
date: 2019-02-21 14:33:54
tags: cors 跨域
---

# 什么是cors
* CORS ，全称是Cross-origin resource sharing，意思是跨域资源共享，众所周知，对于ajax请求，浏览器是有同源策略的限制的，cors是一个W3C标准，用来避开同源策略。简单来说就是为了解决跨域问题的，我们知道，跨域技术有jsonp，但是jsonp只支持get请求，对于一些高级的请求比如post,delete,put等，jsonp就无能为力了，所以cors的优势就体现出来了。

# 如何使用cors
* Cors的使用需要浏览器和服务器端同时配合。目前所有浏览器都支持，其中IE浏览器支持IE10以上。
* 使用cors的整个过程，前端无需做其他处理，正常发ajax请求即可，所以对于前端来说，使用cors通信和同源的ajax没有差别，代码完全一样。
* 浏览器一旦发现ajax请求的是跨域的资源，就会自动添加一些附加的header信息，有时候还会多出一次附加的请求，但是用户不会有察觉。
* 因此，实现cors关键是服务器。只要服务器配置了响应头，就可以实现跨域通信。

浏览器将cors请求分成两类，简单请求和非简单请求。get和post都是简单请求。

## 简单请求：
* 1.在Request Headers中会自动添加一个额外的Origin头部，其中包含请求页面的资源信息（协议，域名和端口），以便服务器根据这个头部信息决定是否给予响应。比如，我在本地搭了一套前端环境，同时搭了一套nodejs后端环境，前端请求页面是http://localhost:9898, 后端服务器地址是http://localhost:7001 。
list接口为http://localhost:7001/user/list, 前端页面发出请求后，控制台request headers如下图。

![1](https://liangzhm.github.io/2019/02/21/Cors%E4%BB%8B%E7%BB%8D%E5%8F%8A%E5%AE%9E%E7%8E%B0%E6%96%B9%E6%B3%95/1.jpg)
 
* 2.如果服务器认为这个请求可以接受，就在response headers里的Access-Control-Allow-Origin 头部中回应相同的源信息。如下图所示。Access-Control-Allow-Origin代表可以同意的跨域请求，这个字段也能设置为*号，代表同意任意的跨域请求。
![2](https://liangzhm.github.io/2019/02/21/Cors%E4%BB%8B%E7%BB%8D%E5%8F%8A%E5%AE%9E%E7%8E%B0%E6%96%B9%E6%B3%95/2.jpg)
 
* 3.如果没有这个头部或者有这个头部但是源信息不匹配，浏览器就会驳回请求。正常情况下，浏览器会处理请求。这里需要注意的是，请求和响应都不包含 cookie 信息。如果当前请求的域名是localhost:9898，但是服务器端返回的Access-Control-Allow-Origin是localhost：9899的话，浏览器发现不一致了，就会报错，报错信息中也显示localhost：9898不允许接入访问（也就是跨域了）。请求头和响应头如下图所示。
 ![3](https://liangzhm.github.io/2019/02/21/Cors%E4%BB%8B%E7%BB%8D%E5%8F%8A%E5%AE%9E%E7%8E%B0%E6%96%B9%E6%B3%95/3.jpg)
报错信息如下图所示：
 ![4](https://liangzhm.github.io/2019/02/21/Cors%E4%BB%8B%E7%BB%8D%E5%8F%8A%E5%AE%9E%E7%8E%B0%E6%96%B9%E6%B3%95/4.png)
* 4.CORS请求是默认不发送Cookie信息的，如果要把cookie 信息发送到服务器，那么需要在ajax 请求时设置 xhr 的属性 withCredentials 为 true，而且服务器端也需要设置响应头部 Access-Control-Allow-Credentials: true。前端代码如下所示：
```
var xhr = new XMLHttpRequest();
xhr.withCredentials = true;
```
* 5.需要注意的是，如果要发送cookie，Access-Control-Allow-Origin就不能设置为*号了，必须指定为与请求的网页一致的域名。而且cookie也是遵循同源策略的，只有在服务器配置Access-Control-Allow-Origin设置过的域名的cookie才会上传，其他域名的cookie并不会上传。

## 非简单请求：
比如请求方法是put或者delete。
> 1.发送预检请求

浏览器在发送真正的请求之前，会先发送一个“预检”请求给服务器。如下图所示，在我点击删除按钮后，浏览器发出两个remove请求。
 ![5](https://liangzhm.github.io/2019/02/21/Cors%E4%BB%8B%E7%BB%8D%E5%8F%8A%E5%AE%9E%E7%8E%B0%E6%96%B9%E6%B3%95/5.jpg)
第一个remove请求就是预检请求，这个请求使用 OPTIONS 方法，表示是来询问的。发送的头信息如下图所示。
 ![6](https://liangzhm.github.io/2019/02/21/Cors%E4%BB%8B%E7%BB%8D%E5%8F%8A%E5%AE%9E%E7%8E%B0%E6%96%B9%E6%B3%95/6.jpg)
Origin：表示来自哪个域名，与简单请求是相同的。
Access-Control-Request-Method: （必须）请求自身使用的方法，当前是delete。
Access-Control-Request-Headers: （可选）自定义的头部信息，多个头部以逗号分隔，默认是conten-type。
> 2.预检请求得到的回应

发送这个请求后，服务器检查上述三个字段后，可以决定是否允许这种类型的跨域请求，可以做出回应。如上图的Response Headers里红色框住的部分，这三个字段都是服务器端返回来的。
•	Access-Control-Allow-Origin：（必须）表示该域名可以请求数据，与简单的请求相同。
•	Access-Control-Allow-Methods: （必须）允许的方法，多个方法以逗号分隔。
•	Access-Control-Allow-Headers: （可选）允许的头部，多个头部以逗号分隔。
> 3.浏览器正常的请求和回应

一旦服务器通过了预检请求，也就是匹配上了，就会正常发出cors请求，和简单请求一样。如下图所示。
 ![7](https://liangzhm.github.io/2019/02/21/Cors%E4%BB%8B%E7%BB%8D%E5%8F%8A%E5%AE%9E%E7%8E%B0%E6%96%B9%E6%B3%95/7.jpg)
再次执行删除操作，还是会发出两次remove请求。
# cors总结
* 优点

>1.CORS 通信与同源的 AJAX 通信没有差别，代码完全一样，容易维护，只是在发送cookie时需要多一行设置xhr的withCredentials为true。
>2.支持所有类型的 HTTP 请求。

* 缺点

>1.存在兼容性问题，特别是 IE10 以下的浏览器。
>2.发送非简单请求时会多一次请求，预检请求。

# 结合业务场景实现的具体代码
本例子是使用基于koa2的eggjs框架实现后端的跨域设置。也可以使用原生nodejs，express，koa2实现，或者使用java实现，实现方法大同小异，都是设置以下三个字段：
```
response.setHeader("Access-Control-Allow-Origin", "*"); // 可接受的域名
response.setHeader("Access-Control-Allow-Methods", "POST,OPTIONS,GET,DELETE");//可接受的请求方法
response.setHeader("Access-Control-Allow-Credentials", "true")// 是否同意接受cookie
```
每种后端语言的设置方式就不一一列举了,下面给出eggjs的跨域设置。

## eggjs跨域设置

* 1.安装egg-cors包
```
  npm install egg-cors --save
```
* 2.在Config/plugin.js里设置
```
exports.cors = {
  enable: true,
  package: 'egg-cors',
};
```
* 3.在config/config.default.js里设置
```
config.cors = {
    origin: 'http://localhost:9898',
    allowMethods: 'GET, POST, PUT, DELETE',
    credentials: true,
  };
```
* 4.设置前端发送cookie到服务器端，由于本例子前端请求使用axios，配置方法如下：
```
axios.defaults.withCredentials = true; //让axios自动发送服务器cookie
```
* 5.实验结果
### 简单请求结果
* 刷新页面，调用list接口，页面发出get请求，返回code：200，请求过程和返回结果如下图。
 ![8](https://liangzhm.github.io/2019/02/21/Cors%E4%BB%8B%E7%BB%8D%E5%8F%8A%E5%AE%9E%E7%8E%B0%E6%96%B9%E6%B3%95/8.jpg)
 ![9](https://liangzhm.github.io/2019/02/21/Cors%E4%BB%8B%E7%BB%8D%E5%8F%8A%E5%AE%9E%E7%8E%B0%E6%96%B9%E6%B3%95/9.png)
### 复杂请求结果
* 点击页面执行删除接口，页面发出delete请求，返回code：200，请求过程和返回结果如下图。
 ![10](https://liangzhm.github.io/2019/02/21/Cors%E4%BB%8B%E7%BB%8D%E5%8F%8A%E5%AE%9E%E7%8E%B0%E6%96%B9%E6%B3%95/10.jpg)
 ![11](https://liangzhm.github.io/2019/02/21/Cors%E4%BB%8B%E7%BB%8D%E5%8F%8A%E5%AE%9E%E7%8E%B0%E6%96%B9%E6%B3%95/11.jpg)

## 结束