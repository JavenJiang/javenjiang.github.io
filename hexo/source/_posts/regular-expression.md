---
title: Regular Expression
date: 2018-04-08
tags: java
---

我们想要解析一段对话：

```
小明说:(2018-07-10 12:00:00)
    今天天气真好
小花说:(2018-07-10 12:00:00)
    是啊，下了一天的雨
```
将他解析成 |人名|时间|内容| 的格式存入数据库中，那么如何做呢?

###一些正则表达式的基本知识:
```
\s 匹配任何空白字符
\S 匹配任何非空白字符
.  匹配除换行符\n之外的任何单字符

```

```
定位符：
^  匹配输入字符串的开始位置
$  匹配输入字符串的结束位置
```

```
限定符：
*  匹配零次或多次
+  匹配一次或多次
?  匹配零次或一次

限定符* +都是贪婪的，他们会尽可能多的匹配文字，只有在他们的后面加上一个?就可以实现非贪婪或最小匹配

贪婪：.*  非贪婪：.*?
```

```
(?=pattern) 正向肯定预查，例如："Windows(?=95|98|NT|2000)"能匹配"Windows2000"中的"Windows"，
            但不能匹配"Windows3.1"中的"Windows"
(?<=pattern) 反向肯定预查，例如："(?<=95|98|NT|2000)Windows"能匹配"2000Windows"中的"Windows"，
            但不能匹配"3.1Windows"中的"Windows"
```

###开始进行解析对话

```
1.我们先将两个语句分开，chatList中存储的即使语句的列表

//获取所有不包含谁谁说的分段聊天List
val sArray = chatRecord.split(".*说:(?=\\()")
//获取所有的谁谁说的List，在和分段聊天List合并
val patternName = Pattern.compile(".*说:(?=\\()")
val m = patternName.matcher(onlineRecord)
var k = 1
while(m.find()){
  chatList.add(m.group() + sArray.apply(k))
  k += 1
}

2.获取括号内的时间:
val pattern2 = new Regex("(?<=\\( )[^\\)]+")

3.获取说话人的名字:
val pattern2 = new Regex("^.*(?=说:\\()")

4.获取对话内容:
val pattern3 = new Regex("(?<=\t)[\\s\\S]*$")
```


注：其实第一步的时候我们还可以使用这种方式将语句分开
```
//    val p : Pattern =
//      Pattern.compile("^.*说:\\(.*\n\t[\\s\\S]*?(?=.*说:)",Pattern.MULTILINE)   //将多条消息切分
//    val onlineRecord = jsonGetSafe(jsonObjData, "online_record")
//    val m = p.matcher(onlineRecord)
//    while(m.find()){
//      chatList.add(m.group())
//    }
//    //最后一条聊天
//    val patternTail = new Regex(".*说:\\(.*\n\t.*$")
//    val recordTail = (patternTail findFirstIn onlineRecord).mkString.trim
//    chatList.add(recordTail)
```
***但是在实际跑数的时候会抛StackOverflow......

一个关于这个问题有很好讲解的链接(其中还有将一些正则表达式的优化)：
> http://zhangner.github.io/2013/03/13/从一个正则表达式造成的StackOverflowError说起/