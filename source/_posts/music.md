---
title: hexo插入音频
date: 2017-08-01 15:42:35
tags:
- 插件
categories: Hexo
---
hexo中插入音频需要用到aplayer 和 dplayer 两个插件实现。
<!-- more -->
插件项目地址
[hexo-tag-aplayer](https://github.com/grzhan/hexo-tag-aplayer#upstream-issue)
[hexo-tag-dplayer](https://github.com/NextMoe/hexo-tag-dplayer)
## 插件安装
```
npm install hexo-tag-aplayer
npm install hexo-tag-dplayer
```
## 插入音乐
### 用法
```
{% aplayer title author url [picture_url, narrow, autoplay, width:xxx, lrc:xxx] %}
```
### 参数
```
title ：音乐标题
author：音乐作者
url：音乐文件网址
picture_url：可选，音乐图片网址
narrow：可选，窄款式
autoplay：可选，自动播放音乐，不支持移动浏览器
width:xxx：可选，前缀width:，播放器宽度（默认值：100％）
lrc:xxx：可选，前缀lrc:，LRC文件url
```
### 单个播放示例
```
{% aplayer "Caffeine" "Jeff Williams" "caffeine.mp3" "picture.jpg" "lrc:caffeine.txt" %}
```
{% aplayer "Paranoid" "Black Sabbath" "http://ou01t80jk.bkt.clouddn.com/Paranoid.mp3" "http://p3.music.126.net/FYW8etVyFbfH8utWYk-KcA==/708085488295457.jpg?param=130y130"  "autoplay=false" %}


### 列表播放示例
```
{% aplayerlist %}
{
	"narrow": false,						
    "autoplay": true,						
    "mode": "random",					
    "showlrc": 2,							
    "mutex": true,						
    "theme": "#e6d0b2",						
	"preload": "metadata",				
	"listmaxheight": "513px",
    "music": [
        {
            "title": "CoCo",
            "author": "Jeff Williams",
            "url": "caffeine.mp3",
            "pic": "caffeine.jpeg",
            "lrc": "caffeine.txt"
        },
        {
            "title": "アイロニ",
            "author": "鹿乃",
            "url": "irony.mp3",
            "pic": "irony.jpg"
        }
    ]
}
{% endaplayerlist %}
```
{% aplayerlist %}
{
	"narrow": false,
    "autoplay": false,		
    "mode": "random",
    "showlrc": 3,
    "mutex": true,
    "theme": "#e6d0b2",
	"preload": "metadata",
	"listmaxheight": "513px",
    "music": [
        {
            "title": "平凡之路",
            "author": "朴树",
            "url": "http://ou01t80jk.bkt.clouddn.com/%E5%B9%B3%E5%87%A1%E4%B9%8B%E8%B7%AF.mp3",
            "pic": "http://p4.music.126.net/W_5XiCv3rGS1-J7EXpHSCQ==/18885211718782327.jpg?param=130y130",
            "lrc": "http://ou01t80jk.bkt.clouddn.com/pfzl.lrc"
        },
        {
            "title": "十年",
            "author": "陈奕迅",
            "url": "http://ou01t80jk.bkt.clouddn.com/%E5%8D%81%E5%B9%B4.mp3",
            "pic": "http://p1.music.126.net/WJgLE2ImnLgqPPLQSlF7iQ==/114349209289440.jpg?param=130y130"
        },
        {
            "title": "匆匆那年",
            "author": "王菲",
            "url": "https://dn-linli580.qbox.me/%E5%8C%86%E5%8C%86%E9%82%A3%E5%B9%B4.m4a",
            "pic": "http://img.1ting.com/images/special/363/s300_82db64700c34712674445e797ce3eb33.jpg"
        },
				{
						"title": "Young For You",
						"author": "GALA",
						"url": "http://ou01t80jk.bkt.clouddn.com/Young%C2%A0For%C2%A0You%20-%20GALA.mp3",
						"pic": "http://p3.music.126.net/_g_52t_RLrYze2dNBkvHvg==/65970697682161.jpg?param=130y130"
				}
    ]
}
{% endaplayerlist %}

## 插入视频
### 示例
```
{%
dplayer "url=http://devtest.qiniudn.com/若能绽放光芒.mp4"
"api=http://dplayer.daoapp.io"
"pic=http://devtest.qiniudn.com/若能绽放光芒.png"
"id=9E2E3368B56CDBB4"
"loop=yes"
"theme=#FADFA3"
"autoplay=false"
"token=tokendemo"
 %}
```

{% dplayer "url=http://devtest.qiniudn.com/若能绽放光芒.mp4" "api=http://dplayer.daoapp.io" "pic=http://devtest.qiniudn.com/若能绽放光芒.png" "id=9E2E3368B56CDBB4" "loop=yes" "theme=#FADFA3" "autoplay=false" "token=tokendemo" %}
