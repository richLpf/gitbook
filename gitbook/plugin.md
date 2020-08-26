# 插件

## 安装插件

```
// book.json文件，见根目录
touch book.json

// 下载插件使用gitbook install,不要使用npm,会出现插件不起作用的情况,可能和网络有关
gitbook install

// 安装插件（不推荐使用）
npm install gitbook-plugin-[插件名]
```

## 常用插件

- 删除原有插件
- 删除自带的gitbook标识
- 安装分享、搜索插件
- 目录插件
  - 目录折叠
  - 页面目录导航
- 插入音频
  - 插入音乐
  - 插入视频
  - 插入图片


## 其他

- 打印项目目录https://www.npmjs.com/package/treer

> 插入音频

```
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=34341360&auto=1&height=66"></iframe>
```

> 视频插入

```
<video id="video" controls="" preload="none" poster="http://om2bks7xs.bkt.clouddn.com/2017-08-26-Markdown-Advance-Video.jpg">
<source id="mp4" src="http://om2bks7xs.bkt.clouddn.com/2017-08-26-Markdown-Advance-Video.mp4" type="video/mp4">
</video>

```

> 插入音乐

```
<audio id="audio" controls="" preload="none">
      <source id="mp3" src="http://oht4nlntk.bkt.clouddn.com/Music_iP%E8%B5%B5%E9%9C%B2%20-%20%E7%A6%BB%E6%AD%8C%20%28Live%29.mp3">
</audio>
```

> github仓库内容

<iframe
    style="margin-left: 2px; margin-bottom:-5px;"
    frameborder="0" scrolling="0" width="100px" height="20px"
    src="https://ghbtns.com/github-btn.html?user=richLpf&repo=gitbook&type=star&count=true" >
</iframe>

## book.json文件

```
{
    "title": "Git入门到进阶",
    "description": "Git入门，Git常用的方法和工作中常见的问题，gitlab、github等仓库的使用",
    "author": "pengfei.lv",
    "language": "zh-hans",
    "root": ".",
    "plugins": [
        "edit-link",  //在github编辑
        "copy-code-button", //复制代码
        "splitter",  //调节左侧边栏
        "pageview-count",  //统计浏览量
        "popup", 
        "-sharing", //删除gitbook自带的分享功能
        "sharing-plus",  //分享插件
        "chapter-fold",  //目录折叠
        "expandable-chapters",  
        "-lunr",   
        "github",  //github图表
        "gittalk",   //github评论
        "anchor-navigation-ex",  //页面导航
        "simple-page-toc" // 页面目录
    ],
    "pluginsConfig": {
        "github-buttons": {
            "repo": "richLpf/gitbook",
            "types": [
                "star"
            ],
            "size": "small"
        },
        "edit-link": {
            "base": "https://github.com/richLpf/gitbook",
            "label": "编辑当前页面"
        },
        "sharing": {
            "all": ["weibo", "qq", "qzone", "douban"]
        },
        "chapter-fold":{},
        "github": {
            "url": "https://github.com/richLpf/gitbook"
        },
        "simple-page-toc": {
          "maxDepth": 3,
          "skipFirstH1": true
        },
        "gittalk": {
          "clientID": "e893b71fc36998d82f8e",
          "clientSecret": "d25b2490666b9105dea0c7ecfbae294a4eef9caa",
          "repo": "gitbook",
          "owner": "richLpf",
          "admin": [
            "richLpf"
          ],
          "distractionFreeMode": false
        },
        "anchor-navigation-ex": {
          "associatedWithSummary": true
        }
    }
}
```

## 各个插件展示

### 目录相关内容

#### "simple-page-toc"生成右侧悬浮目录

```
{
    "plugins": [
        "page-treeview"
    ],
    "pluginsConfig": {
        "page-treeview": {
            "copyright": "Copyright &#169; aleen42",
            "minHeaderCount": "2",
            "minHeaderDeep": "2"
        }
    }
}
```
如图所示：gitbook-menu.png

#### "page-treeview"生成页面头部目录

```
{
    "plugins": [
        "page-treeview"
    ],
    "pluginsConfig": {
        "page-treeview": {
            "copyright": "Copyright &#169; aleen42",
            "minHeaderCount": "2",
            "minHeaderDeep": "2"
        }
    }
}

```
如图所示：


#### flexible-alerts 警报

```
> [!NOTE]
> 这是一个简单的Note类型的使用，所有的属性都是默认值。

```


## 静态文件利用github进行评论的方法`gittalk`

```
"gittalk": {
      "clientID": "e893b71fc36998d82f8e",
      "clientSecret": "d25b2490666b9105dea0c7ecfbae294a4eef9caa",
      "repo": "gitbook",
      "owner": "richLpf",
      "admin": [
        "richLpf"
      ],
      "distractionFreeMode": false
    }
```

参数获取方法：

