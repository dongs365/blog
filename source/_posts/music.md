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
# 插件安装
```
npm install hexo-tag-aplayer
npm install hexo-tag-dplayer
```
## 插入音乐
#### 用法
```
{% aplayer title author url [picture_url, narrow, autoplay, width:xxx, lrc:xxx] %}
```
#### 参数
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
#### 单个播放示例
```
{% aplayer "Caffeine" "Jeff Williams" "caffeine.mp3" "picture.jpg" "lrc:caffeine.txt" %}
```
{% aplayer "匆匆那年" "王菲" "http://96.f.1ting.com/59803334/120d6a57e74245233e95af095a2c56c3/zzzzzmp3/2014lDec/30F/30ccnn/20.mp3" "http://img.1ting.com/images/special/363/s300_82db64700c34712674445e797ce3eb33.jpg"  "autoplay=false" %}


#### 列表播放示例
```
{% aplayerlist %}
{
	"narrow": false,						
    "autoplay": true,						
    "mode": "random",					
    "showlrc": 3,							
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
            "title": "Paranoid",
            "author": "Black Sabbath",
            "url": "http://m10.music.126.net/20170801163159/cb153664fe3a17309c91edb1c5a936a1/ymusic/11a1/3a12/6b79/5dd9b8946fcef55201d76d75d3286f26.mp3",
            "pic": "http://p4.music.126.net/FYW8etVyFbfH8utWYk-KcA==/708085488295457.jpg?param=130y130",
            "lrc": "caffeine.txt"
        },
        {
            "title": "有一个地方叫远方",
            "author": "曾昭玮",
            "url": "http://96.f.1ting.com/59803588/197950b06e01ceecf04252ea22707b78/zzzzzmp3/2017gJul/10X/10c_Zhaowei/01.mp3",
            "pic": "http://img.1ting.com/images/special/414/s300_2b6454b05ce689dfc2f49e9bbf9f4e31.jpg"
        },
        {
            "title": "匆匆那年",
            "author": "王菲",
            "url": "http://96.f.1ting.com/59803334/120d6a57e74245233e95af095a2c56c3/zzzzzmp3/2014lDec/30F/30ccnn/20.mp3",
            "pic": "http://img.1ting.com/images/special/363/s300_82db64700c34712674445e797ce3eb33.jpg"
        }
    ]
}
{% endaplayerlist %}

## 插入视频
#### 示例
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
