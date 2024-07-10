# docker学习笔记

在上文中，服务器上搞好focker了，下载了一个windTerm，第一次使用第三方远程工具。这玩意有点丑，不过也不需要每次打开阿里云官网，打开云服务器再连接

![image-20240708151322745](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240708151322745.png)



##### 搜索是否有这个镜像（非本地）

```shell
docker search nginx
```

name表示名称，description表示描述 ，offical表示是否为官方镜像

![image-20240708151951165](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240708151951165.png)

##### 下载镜像

```
docker pull nginx
```

- 下载指定版本镜像，浏览官网查找完整镜像标签。完整下载版命令（从hub上找）

  - ```
    docker pull nginx:1.26.0
    ```

https://hub.docker.com/

<img src="docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240708161700710.png" alt="image-20240708161700710" style="zoom:50%;" />

在Tags中寻找镜像版本，复制命令

<img src="docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240708161904494.png" alt="image-20240708161904494" style="zoom:50%;" />

- 如果不能拉取成功，那么就配置镜像加速试试（我真搞不明白。不是说已经全盘崩掉了吗，为什么还能用啊，我想了好久为什么官网的为什么不能用，原来是服务器是远程的！！！心碎，只好试试镜像加速行不行，结果行？这是为什么）

```shell
\# 配置加速

sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'

{

  "registry-mirrors": ["https://82m9ar63.mirror.aliyuncs.com"]

}

EOF

sudo systemctl daemon-reload

sudo systemctl restart docker
```

##### 检查系统是否有这个镜像

```shell
docker images
docker images ls  // 完整查看所有镜像（包括版本号不同的）
```

![image-20240708161126470](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240708161126470.png)

从左到右分别是：镜像名、版本（lastest为最新，但目前这种情况也已经无法确定了）、镜像唯一ID、创建时间、大小

##### 删除镜像

```
docker rmi nginx:latest       // 完整名称来删除

docker rmi 605c77e624dd  // 利用唯一Id删除
```

## 容器操作

##### 运行容器

```
docker run nginx:latest
```

如果不指定版本号，那么就默认使用最新镜像，而不是已有的镜像。如果没有的话，则会自动下载

##### 查看运行中的应用

```
docker ps
```

![image-20240708164354162](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240708164354162.png)

从左到右，容器唯一ID、镜像名和版本号，启动命令，创建时间、状态（up表示没问题，Exited为退出）、端口号、容器名（随机）

##### 查看所有容器，包括未启动的容器

```
docker ps -a  
```

启动容器

```
docker start nginx

docker start 2ae  // 可只写三位，如果能同其他镜像区分
```

##### 停掉

```
docker stop nginx

//同上，或使用应用名NAMES
```

##### 重启

重启后唯一ID和容器名似乎会再次分配

```
docker restart 2ae
```

##### 查看应用占用情况

```
docker stats 2aex
```

每秒发生变化

![image-20240708165914050](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240708165914050.png)

##### 查看日志

```
docker logs 2a
```

##### 删除容器

```
docker rm 2a
```

注意：如果想删除一个容器，必须要将容器停了才能删除

或强制删除

```sh
docker rm -f 2a
```

注意区别，rm删除容器，rmi删除镜像

一次删除所有容器

```sh
docker rm -f $(docker ps -aq)
```



## run细细道来

```sh
docker run -d --name yourname -p 2348:80 nginx
```

-d ：后台运行

--name 为容器定义名字

-p 端口号映射 容器内映射到本机 **（！！是将容器内80端口映射到服务器2348端口，注意顺序）**

![image-20240708172302412](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240708172302412.png)

![image-20240708202424890](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240708202424890.png)

启动后会打印完整用户ID，用ps查看后发现，唯一ID已经发生变化

##### 报错解决

![image-20240708201759007](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240708201759007.png)

说明端口号被占用

```sh
netstat -tanlp  #查看占用端口命令
```

![image-20240708201912921](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240708201912921.png)

杀死进程

```sh
kill 735
```

即可再次创建docker容器

###### next：出现如下报错

```
tcpdocker: Error response from daemon: driver failed programming external connectivity on endpoint mynginx2 (7501b9388a430f5057abce361eb7c970f223cdac69033e8f0728cb993c1261f8):  (iptables failed: iptables --wait -t nat -A DOCKER -p tcp -d 0/0 --dport 2349 -j DNAT --to-destination 172.17.0.3:80 ! -i docker0: iptables: No chain/target/match by that name.
```

运行如下

```sh
systemctl restart docker
```



##### 进入容器

```sh
docker exec -it ac /bin/bash
```

![image-20240708203254847](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240708203254847.png)

`exit` 退出容器内部

## 保存镜像

##### 提交镜像

```sh
docker commit
```

创建一个镜像，可以将容器内部情况全部复制，打包成一个新的镜像

![image-20240710103807250](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710103807250.png)

-a 指定作者

-c 有哪些改变的列表

-m 此次提交的信息

-p 打包期间暂停容器运行

```
docker commit  -m "提交信息" mynginx mynginx:v1.0
```

##### 检查镜像列表

![image-20240710104146893](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710104146893.png)

##### 保存镜像

```
docker save
```

![image-20240710104311397](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710104311397.png)

![image-20240710104232104](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710104232104.png)

##### 加载镜像

```
docker load
```

![image-20240710104457599](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710104457599.png)

![image-20240710104436077](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710104436077.png)

## 分享社区

##### 登陆 docker login

![image-20240710104821446](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710104821446.png)

##### 为镜像改名

```
docker tag
```

上传的镜像为用户名加镜像名，故上传前需要用docker tag 进行改名，下图显示docker tag 的用法以及改名后虽然名字不同，但唯一ID是一样的，说明是同一个镜像

![image-20240710105002484](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710105002484.png)

**制作一个最新版本，便于在社区下载**

![image-20240710105525240](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710105525240.png)

##### 推送镜像

```
docker push
```

（此时不能写镜像id，因为一样而无法区分）

![image-20240710105235244](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710105235244.png)

## 存储-目录挂载

##### 目录挂载：

```
 -v /app/nghtml:/usr/share/nginx/html
```

  （本机目录->容器目录，以本机目录为主，新建后如果本机目录没有东西则容器内页也没有，用在docker run上）

卷映射：

```
-v ngconf:/etc/nginx  
```

（外部目录以内部目录为准）

目录统一挂载到本机`/var/lib/docker/volumes/<volume-name>`   （上方卷映射卷名为ngconf）

删除容器时，卷不会被删除，数据依然存在

##### 列出所有的卷

```
docker volume ls
```

##### 手动创建卷

```
docker volume create  <volume-name> 
```

##### 查看某一个卷的详情

```
docker volume inspect <volume-name> 
```

下图打印卷所在的真正位置

![image-20240710111912323](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710111912323.png)

## 自定义网络

docker为每个容器分配唯一ip，使用 **容器ip+容器端口** 可以互相访问

ip由于各种原因可能会变化,docker()默认不支持主机域名访问，故使用自定义网络。

创建自定义网络后，容器名就是稳定域名

##### 创建自定义网络

```
docker network create mynet
```

##### 查看所有网络

```
docker network ls
```

![image-20240710113948042](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710113948042.png)

**bridge桥接网络**

当创建好自定义网络后，需要在docker run中使用，将容器添加到自定义网络

```
docker run -d -p 2341:80 --name myapp1 --network mynet nginx
```

-e 添加环境变量

##### 查看容器详情

```
docker inspect myapp1 
```

![image-20240710114824261](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710114824261.png)

![image-20240710114905130](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710114905130.png)

