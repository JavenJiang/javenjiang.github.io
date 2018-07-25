---
title: Redis
date: 2018-05-13
tags: Redis
---
Redis分布式锁:

目的：使用redis分布式锁为了保护接口，当有大量相同请求同时进入，避免大量请求对服务造成过大压力

实现方案：
```
    public static String getValueLock(JedisCluster jedisCluster, String key, long sleepMilliseconds, long cacheLockMilliseconds) throws Exception {
        //如果已经缓存，则直接从缓存中取出结果返回
        String resContent = jedisCluster.get(key);
        //未缓存则需用到redis分布式锁来保护程序
        if (resContent == null) {
            //加锁，set方法的参数详见其文档
            String result =	jedisCluster.set(key + keyOfInquery,"Y", "NX","PX",cacheLockMilliseconds);
            //第一个加上锁的，会返回"OK",return离开方法，去进行数据的请求(若未set成功，即redis中key-value已存在，则会返回null)
            if( null == result || !result.equals("OK")) {
                //剩余的请求会在这里循环等待，直到那个加上锁的将数据写入redis缓存或者超时抛出异常
                for (int counter = 0; counter < 1000; counter++) {
                    resContent = jedisCluster.get(key);
                    if (resContent != null) {
                        return resContent;
                    } else {
                        Thread.sleep(sleepMilliseconds);
                    }
                }
                throw new Exception("Redis Lock Exception:Sleep over " + sleepMilliseconds + "*5000 milliseconds,the key: " + key);
            }
        }
        return resContent;
    }
```
redis 有序集合
redis 有序列表
