---
title: Linux
date: 2018-01-10
tags: linux
---
## CPU飘高问题查找
```

top -> 找出cpu占用率最高的进程PID

top -H -p [pid] -> 根据PID找出占用率最大的线程PID

jstack -l [pid] >  [pid].stack 得到进程的线程堆栈

vim [pid].stack -> /ThreadPID的16进制
```
> 引用: https://blog.csdn.net/posonrick/article/details/79752208

## Date traversal script
```
for i in $(seq 1 31) //反向 for i in {31..1}
do
if [ ${i} -le 9 ]
then
i="0"${i}
fi
sh xx.sh
sleep 1m
done
```

```
end=`date -d '30 day ago' "+%Y-%m-%d"`
start=`date -d '2 day ago' "+%Y-%m-%d"`

#start=`date +%Y-%m-%d`
while [ "${start}" != "${end}" ]
do
  echo `date +"%Y-%m-%d %H:%M:%S"`
  
  sleep 5m
  start=$(date -d "-1 day ${start}" +%Y-%m-%d)
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
    
curl -XGET -H 'Content-Type:application/json' "es-sql:......" > data.txt

```

## Regular Expression

```
//取出json中value:后面的值(这么写是因为能保证value:在结果中只有一个)
numResult=$(echo ${jsonResult} | grep -P '(?<="value:")[0-9]*' -o)
```

## awk

```
//文件中是 id amount 这样的数据，将所有的amount加和
es_total_amount=$(awk -F'\t' 'BEGIN{
     es_total_amount=0
}{
     es_total_amount=es_total_amount+$2
}
END{printf("%d\n",es_total_amount);}' /data/monitor_es_result.txt)

//计算行数加和
es_id_count=$(awk -F'\t' 'BEGIN{
     es_id_count=0
}{
     es_id_count++
}
END{print es_id_count;}' /data/monitor_es_result.txt)


awk -F'\t' 'BEGIN{
    while((getline<"/data/content.txt")){ zhengwen[$1]=$2;title[$1]=$3;}
}
{
    zhengwen_item = zhengwen[$2];
    title_item = title[$2];
    ......

```