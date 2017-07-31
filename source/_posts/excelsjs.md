---
title: Excel 随机字符数
date: 2017-07-31 15:04:32
tags:
- excel
- offices
categories: Windows
---
Excle随机字符数生成，用于密码生成等操作。<!-- more -->

函数：MID
功能：从一个文本字符串的指定位置开始，截取指定数目的字符。
语法：
```
MID(text,start_num,num_chars)
```

函数：INT
功能：将数字向下舍入到最接近的整数。
语法：
```
INT(number)
```
Number  需要进行向下舍入取整的实数。

函数：RAND
功能：返回大于或等于 0 且小于 1 的平均分布的随机数字。每次重新计算包含此函数的单元时，返回的数字都会更改。
语法：
```
RANK(number,ref,order)
```
Number 为需要找到排位的数字。
Ref 为数字列表数组或对数字列表的引用。Ref 中的非数值型参数将被忽略。
Order 为一数字，指明排位的方式。


#### 随机生成数字
```
=MID("0123456789",INT(RAND()*10)+1,1)
```
#### 随机生成小写字母
```
=MID("abcdefghijklmnopqrstuvwsyz",INT(RAND()*26)+1,1)
```
#### 随机生成大写字母
```
=MID("ABCDEFGHIJKLMNOPQRSTUVWXYZ",INT(RAND()*26)+1,1)
```
#### 随机生成大小写字母
```
=MID("0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwsyz",INT(RAND()*62)+1,1)
```
#### 生成6位字符的随机字符
```
=MID("0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwsyz",INT(RAND()*62)+1,1)&=MID("0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwsyz",INT(RAND()*62)+1,1)&=MID("0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwsyz",INT(RAND()*62)+1,1)&=MID("0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwsyz",INT(RAND()*62)+1,1)&=MID("0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwsyz",INT(RAND()*62)+1,1)&=MID("0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwsyz",INT(RAND()*62)+1,1)
```
