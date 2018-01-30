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

{% aplayer "Paranoid" "Black Sabbath" "http://ou01t80jk.bkt.clouddn.com/Paranoid.mp3" "http://p3.music.126.net/FYW8etVyFbfH8utWYk-KcA==/708085488295457.jpg?param=130y130" lrc:"http://ou01t80jk.bkt.clouddn.com/Paranoid.lrc" "autoplay=false" %}


### 列表播放示例
```
{% aplayerlist %}
{
	"narrow": false,						
    "autoplay": true,						
    "mode": "random",					
    "showlrc": 1,							
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
    "showlrc": 1,
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
            "lrc": "[ti:平凡之路]\n[ar:朴树]\n[al:后会无期主题歌]\n[00:00.31]后会无期主题歌\n[00:02.41]朴树 - 平凡之路\n[00:03.76]作词：韩寒 朴树 \n[00:05.67]作曲：朴树 编曲：朴树\n[00:12.12]徘徊着的  在路上的  \n[00:17.47]你要走吗\n[00:23.30]易碎的  骄傲着  \n[00:28.85]那也曾是我的模样\n[00:34.65]沸腾着的  不安着的  \n[00:40.36]你要去哪\n[00:46.10]谜一样的  沉默着的  \n[00:51.85]故事你真的在听吗\n[00:56.35]我曾经跨过山和大海  \n[00:59.65]也穿过人山人海\n[01:02.80]我曾经拥有着一切     \n[01:05.10]转眼都飘散如烟\n[01:07.85]我曾经失落失望失掉所有方向\n[01:13.56]直到看见平凡才是唯一的答案\n[01:31.80]当你仍然  \n[01:33.20]还在幻想  \n[01:37.50]你的明天\n[01:43.10]她会好吗  还是更烂  \n[01:49.88]对我而言是另一天\n[01:53.43]我曾经毁了我的一切  \n[01:56.64]只想永远地离开\n[01:59.92]我曾经堕入无边黑暗   \n[02:02.24]想挣扎无法自拔\n[02:04.89]我曾经象你象他象那野草野花\n[02:10.64]绝望着 渴望着 \n[02:13.64]也哭也笑平凡着\n[03:03.48]向前走 就这么走   \n[03:06.33]就算你被给过什么\n[03:09.18]向前走 就这么走   \n[03:11.93]就算你被夺走什么                                        \n[03:14.88]向前走 就这么走   \n[03:17.68]就算你会错过什么\n[03:20.43]向前走 就这么走   \n[03:23.23]就算你会\n[03:25.88]我曾经跨过山和大海  \n[03:28.24]也穿过人山人海\n[03:30.54]我曾经拥有着一切     \n[03:33.79]转眼都飘散如烟\n[03:36.34]我曾经失落失望失掉所有方向\n[03:42.14]直到看见平凡才是唯一的答案\n[03:47.79]我曾经毁了我的一切  \n[03:50.94]只想永远地离开\n[03:53.49]我曾经堕入无边黑暗   \n[03:56.39]想挣扎无法自拔\n[03:59.14]我曾经象你象他象那野草野花\n[04:04.89]绝望着 渴望着 也哭也笑平凡着\n[04:10.74]我曾经跨过山和大海  \n[04:13.64]也穿过人山人海\n[04:16.24]我曾经问遍整个世界  \n[04:19.59]从来没得到答案\n[04:22.98]我不过象你象他象那野草野花\n[04:27.74]冥冥中这是我 唯一要走的路啊\n[04:34.75]时间无言  \n[04:36.25]如此这般\n[04:40.40]明天已在眼前\n[04:46.55]风吹过的  路依然远\n[04:51.65]你的故事讲到了哪\n"
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
dplayer "url=http://ou01ijyji.bkt.clouddn.com/fuckyou.mp4"
"api=http://dplayer.daoapp.io"
"pic=http://p1.music.126.net/8qHI7sEEk3eM2DLitQXFDQ==/765260092953631.jpg?param=130y130"
"id=9E2E3368B56CDBB4"
"loop=yes"
"theme=#FADFA3"
"autoplay=false"
"token=tokendemo"
 %}
```

{% dplayer "url=http://ou01ijyji.bkt.clouddn.com/fuckyou.mp4" "api=http://dplayer.daoapp.io" "pic=http://p1.music.126.net/8qHI7sEEk3eM2DLitQXFDQ==/765260092953631.jpg?param=130y130" "id=9E2E3368B56CDBB4" "loop=yes" "theme=#FADFA3" "autoplay=false" "token=tokendemo" %}
