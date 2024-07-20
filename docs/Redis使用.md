# Redis使用

来自博文：http://t.csdnimg.cn/6Pmpz

没有使用window系统下的Linux子系统，使用了[zkteco-home ](https://github.com/zkteco-home) 维护的 Redis。

地址：https://github.com/zkteco-home/redis-windows/releases



尚未将Redis随系统自启动，已添加环境变量

（1）下载安装包


（2）解压安装包

（3）启动Redis服务
打开cmd窗口，切换到Redis安装路径，输入 

```
redis-server
```


 启动 redis 服务



（4） **连接Redis服务**
再打开一个新的cmd窗口，切换到Redis安装路径，输入

```
 redis-cli 
```

连接到 Redis 服务



（5）**停止或删除**Redis服务

a. 使用 sc stop 命令停止(禁用)服务

```vbscript
sc stop Redis
```

b. 使用 sc delete 命令删除服务

（sc stop 停止服务后再执行删除操作）

```typescript
sc delete Redis
```



原文链接：https://blog.csdn.net/m0_60416689/article/details/135727013