# docker学习笔记

在上文中，服务器上搞好focker了，下载了一个windTerm，第一次使用第三方远程工具。这玩意有点丑，不过也不需要每次打开阿里云官网，打开云服务器再连接

![image-20240708151322745](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240708151322745.png)



##### 搜索是否有这个镜像

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
docker stats 2ae
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

```
docker rm -f 2a
```

注意区别，rm删除容器，rmi删除镜像

## run细细道来

```
docker run -d --name yourname -p 80:2348 ngnix
```

-d ：后台运行

--name 为容器定义名字

-p 端口号映射 容器内映射到本机

![image-20240708172302412](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240708172302412.png)

启动后会打印完整用户ID，用ps查看后发现，唯一ID已经发生变化
