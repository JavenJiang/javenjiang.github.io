---
title: Linux
date: 2018-01-10
tags: linux
---

## Date traversal script
```
for i in $(seq 1 31)
do
if [ ${i} -le 9 ]
then
i="0"${i}
fi
sh xx.sh
sleep 1m
done
```

## Crontab

```
crontab -l 查看所有设置的定时任务列表
crontab -e 编辑定时任务列表

第1列分钟0～59
第2列小时0～23（0表示子夜）
第3列日1～31
第4列月1～12
第5列星期0～7（0和7表示星期天）
第6列要运行的命令

例子: 每周日18:00到23:00每隔30分钟执行一次
*/30 18-23 * * 0 xx.sh
0,30 18-23 * * 7 xx.sh
```

## Curl:
```
//get请求:
curl http://www.baidu.com
```

```
//linux使用curl发出post请求从es中取出数据（es-sql）
jsonResult=$(
curl -H "Content-Type: application/json" 
     -XPOST 'http://ip:port/_sql' 
     -d "select count(*) from table_name")

```

## Regular Expression

```
//取出json中value:后面的值(这么写是因为能保证value:在结果中只有一个)
numResult=$(echo ${jsonResult} | grep -P '(?<="value:")[0-9]*' -o)
```

## awk

```

```