## 常见知识点汇总：

判断移动端和方法

```js
if(/Android|webOS|iPhone|iPod|BlackBerry/i.test(navigator.userAgent)) {
    window.location.href = "https://www.baidu.com/";
} else {
    window.location.href = "http://news.baidu.com/";
}
```

如何判断页面是否嵌套在iframe中，如何将禁止

```
// 具体需要查查
x-iframe-options: false
1.方式一

if (self.frameElement && self.frameElement.tagName == "IFRAME") {
　　alert('在iframe中');
}

2.方式二
if (window.frames.length != parent.frames.length) {
　　alert('在iframe中');
}

3.方式三
if (self != top) { 
　　alert('在iframe中');
}
```

