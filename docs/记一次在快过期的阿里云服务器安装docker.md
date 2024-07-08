#### 记一次在快过期的阿里云服务器安装docker

首先是查看当初购买的服务器是什么版本的，这可真是一个莫名其妙的东西。

![image-20240708133245474](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240708133245474.png)

在docker官网上根本找不到这玩意

<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240708133342969.png" alt="image-20240708133342969" style="zoom:50%;" />

由此，在这个服务器上安装docker需要用上两份说明文档

~~https://docs.docker.com/engine/install/centos/~~和[安装Docker并使用_云服务器 ECS(ECS)-阿里云帮助中心 (aliyun.com)](https://help.aliyun.com/zh/ecs/use-cases/install-and-use-docker-on-a-linux-ecs-instance?spm=a2c4g.11186623.0.0.4432e6dfnBe4Dp#471afb3005tua)

~~（因为国内全部docker镜像已经不能用了）~~  好吧，我后来查看了阿里云这篇文档，刚刚好在在今天早上被更新过，原来不需要官方文档了，

运行以下命令，添加docker-ce的dnf源。

```shell
sudo dnf config-manager --add-repo=https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

运行以下命令，安装Alibaba Cloud Linux 3专用的dnf源兼容插件。

```shell
sudo dnf -y install dnf-plugin-releasever-adapter --repo alinux3-plus
```

运行以下命令，安装Docker。

```shell
sudo dnf -y install docker-ce --nobest
```

如果执行命令时，出现类似如下的报错信息，您需要执行`sudo dnf clean packages`清除软件包缓存后，重新安装docker-ce。

```shell
(8-9/12): docker-ce-24.0.7-1.el8.x86_64.rpm 38% [================- ] 8.2 MB/s | 38 MB 00:07 ETA
The downloaded packages were saved in cache until the next successful transaction.
You can remove cached packages by executing 'dnf clean packages'.
Error: Error downloading packages:
containerd.io-1.6.26-3.1.el8.x86_64: Cannot download, all mirrors were already tried without success
```

如果执行命令时，出现类似下图的报错信息，您需要注释/etc/yum.repos.d下的CentOS源，注释后重新安装docker-ce。

![img](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/1286511071/p477222.png)

执行以下命令，检查Docker是否安装成功。

```javascript
docker -v
```

如下图回显信息所示，表示Docker已安装成功。

![image..png](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6489086861/p683254.png)

执行以下命令，启动Docker服务，并设置开机自启动。

```shell
sudo systemctl start docker
sudo systemctl enable docker
```

执行以下命令，查看Docker是否启动。

```shell
sudo systemctl status docker
```

如下图回显所示，表示Docker已启动。

![image..png](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9710116861/p679571.png)