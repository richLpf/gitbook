# 插件

## 安装插件

```
//book.json文件，见根目录
touch book.json

npm install gitbook-plugin-[插件名]

// 下载插件使用gitbook install,不要使用npm,会出现插件不起作用的情况
gitbook install
```

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
        "simple-page-toc"
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