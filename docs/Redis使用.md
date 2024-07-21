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





# 设置Redis后台启动（失败）

1. 打开Redis的配置文件 redis.conf。在这个文件中，可以找到并修改一些配置选项。

2. 在配置文件中找到**daemonize**选项。将该选项的值改为yes。这会使Redis以守护进程的形式在后台运行。

   没有则新建一个：

   ```
   daemonize yes
   ```

3. 如果Redis需要身份验证，需要在配置文件中找到requirepass选项，并设置一个密码。

4. 保存并关闭配置文件。

5. 启动Redis。可以使用以下命令启动Redis（**在redis目录下**）：

   ```shell
   redis-server ./redis.conf
   ```

   这里的 `./redis.conf` 是redis.conf文件的路径。

   另外，**如果Redis的可执行文件没有设置到环境变量中**，你可能需要指定完整的Redis可执行文件的路径。

6. 确认Redis已经成功启动。可以使用以下命令检查Redis的运行状态：

   ```shell
   redis-cli ping
   ```

   如果返回 "**PONG**"，则表示Redis已经成功启动并在后台运行。

7. 如果需要停止Redis后台运行，可以使用以下命令：

   ```
   redis-cli shutdown
   ```

   这会向Redis发送一个停止信号，Redis会优雅地关闭。

```shell
SC CREATE Redis binpath= "\"D:\JDK\redis-windows-7.2.5.0\redis-server.exe\" --service-run \"D:\JDK\redis-windows-7.2.5.0\redis.conf\"" start= auto
```

```
SC CREATE Redis binpath= "\"D:\JDK\redis-windows-7.2.5.0\redis-server.exe\" --service-run \"D:\JDK\redis-windows-7.2.5.0\redis.conf\"" start= auto
```



```json
{"@type":"com.irreplace.domain.entity.LoginUser",
"accountNonExpired":true,"
accountNonLocked":true,"
 credentialsNonExpired":true,"
 enabled":true,"
 password":"$2a$10$Jnq31rRkNV3RNzXe0REsEOSKaYK8UgVZZqlNlNXqn.JeVcj2NdeZy",
 "user":{"avatar":"https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fi0.hdslb.com%2Fbfs%2Farticle%2F3bf9c263bc0f2ac5c3a7feb9e218d07475573ec8.gi","createTime":1641344516000,"delFlag":0,"email":"23412332@qq.com","id":"1","nickName":"sg333","password":"$2a$10$Jnq31rRkNV3RNzXe0REsEOSKaYK8UgVZZqlNlNXqn.JeVcj2NdeZy","phonenumber":"18888888888","sex":"1","status":"0","type":"1","updateBy":"1","updateTime":1643528223000,"userName":"sg"},"username":"sg"}
```

