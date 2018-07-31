---
title: PHP常用字符串函数
date: 2017-11-22 20:00:47
tags: PHP
categories: PHP
---

本文列举常用的PHP字符串函数

<!-- more -->

##	查找字符位置函数

```
strpos($str,search,[int])://查找search在$str中的第一次位置从int开始；
strrpos($str,search,[int])://查找search在$str中的最后一次出现的位置从int开始
```

##	提取子字符函数（双字节）

```
submit($str,int start[,int length])://从$str中strat位置开始提取[length长度的字符串]。
strstr($str1,$str2)://从$str1(第一个的位置)搜索$str2并从它开始截取到结束字符串;若没有则返回FALSE。
stristr()://功能同strstr，只是不区分大小写。
strrchr()://从最后一次搜索到的字符处返回；用处：取路径中文件名
```

##	替换字符串

```
str_replace(search,replace,$str):从$str中查找search用replace来替换
str_irreplace(search,replace,$str):
strtr($str,search,replace):这个函数中replace不能为"";
substr_replace($Str,$rep,$start[,length])$str原始字符串,$rep替换后的新字符串,$start起始位置,$length替换的长度，该项可选
```

##	查询字符串长度

```
int strlen($str)
```

##	比较字符函数

```
int strcmp($str1,$str2):$str1>=<$str2分别为正1,0,-1（字符串比较)
strcasecmp() 同上:(不分大小写)
strnatcmp("4","14") :按自然排序比较字符串
strnatcasecmp() :同上,(区分大小写）
```

##	分割成数组函数

```
str_split($str,len):把$str按len长度进行分割返回数组
split(search,$str[,int]):把$str按search字符进行分割返回数组int是分割几 次，后面的将不分割
explode(search,$str[,int])
```

##	去除空格：

```
ltrim:去除字符串左边空格
rtrim:去除字符串右边边空格
trim:去除字符串两边空格
```

##	加空格函数

```
chunk_split($str,2);向$str字符里面按2个字符就加入一个空格;
```

##	返回指定的字符或ascii

```
chr、ord
```

##	HTML代码有关函数

```
nl2br():使\n转换为<br>。
strip_tags($str[,'<p>'])://去除HTML和PHP标记
htmlspecialchars($str[,参数])://页面正常输出HTML代码参数是转换方式
```

##	字符大小写转换函数

```
strtolower($str) :字符串转换为小写
strtoupper($str) :字符串转换为大写
ucfirst($str) :将函数的第一个字符转换为大写
ucwords($str) :将每个单词的首字母转换为大写
```

##	数据库相关函数

```
addslashes($str):使str内单引号（'）、双引号（"）、反斜线（\）与 NUL字符串转换为\',\",\\。
magic_quotes_gpc = On :自动对 get post cookie的内容进行转义
get_magic_quotes_gpc（）:检测是否打开magic_quotes_gpc
stripslashes() :去除字符串中的反斜杠
```

##	连接函数

```
implode(str,$arr) :将字符串数组按指定字符连接成一个字符串；
implode():函数有个别名函数join
```

















