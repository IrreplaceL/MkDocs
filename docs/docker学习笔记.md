

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

<img src="docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710114824261.png" alt="image-20240710114824261" style="zoom:50%;" />

<img src="docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710114905130.png" alt="image-20240710114905130" style="zoom:50%;" />



## docker compose

<img src="docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710195653829.png" alt="image-20240710195653829" style="zoom:67%;" />

<img src="docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710195855972.png" alt="image-20240710195855972" style="zoom:67%;" />

创建自定义网络

```sh
docker network create blog
```

创建mysql数据库（\为换行）

```sh
docker run -d -p 3307:3306 \-e MYSOL_ROOT_PASSWORD=123456 -e MYSQL_DATABASE=wordpress -v mysql-data:/var/lib/mysql -v/app/myconf:/etc/mysql/conf.d \--restart always --name mysql --network blog mysql:8.0
```

创建wordpress

```sh
\#启动wordpress

docker run -d -p 8080:80 \

-e WORDPRESS_DB_HOST=mysql \

-e WORDPRESS_DB_USER=root \

-e WORDPRESS_DB_PASSWORD=123456 \

-e WORDPRESS_DB_NAME=wordpress \

-v wordpress:/var/www/html \

--restart always --name wordpress-app \

--network blog \

wordpress:latest
```

### 使用compose.yaml启动容器

![image-20240710204606174](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710204606174.png)

编辑一个yarl文件

```
vim compose.yaml
```

进入编辑compose文件中，将下方代码粘贴，随后`:wq`退出保存，利用`ls`查看是否存在该文件

```yaml
name: myblog     #compose名称
services:        #  服务，这个yaml文件中启用两个镜像，一个是mysql，一个是博客镜像wordpress 
  mysql:			#mysql镜像配置
    container_name: mysql     
    image: mysql:8.0      #所使用的镜像版本
    ports:					#端口号配置
      - "3307:3306"			#数组的写法
    environment:			#环境变量
      - MYSQL_ROOT_PASSWORD=123456
      - MYSQL_DATABASE=wordpress
    volumes:					#挂载
      - mysql-data:/var/lib/mysql    #卷挂载需要在最外层指定
      - /app/myconf:/etc/mysql/conf.d 
    restart: always       #开机自动启动
    networks:          #加入到自定义网络，该网络也需要在最外层指定
      - blog

  wordpress:
    image: wordpress
    ports:
      - "8081:80"
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_USER: root
      WORDPRESS_DB_PASSWORD: 123456
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wordpress:/var/www/html
    restart: always
    networks:
      - blog
    depends_on:			#该镜像依赖于mysql，需要mysql启动后才能docker run这个
      - mysql

volumes:            #指定卷挂载
  mysql-data:
  wordpress:

networks:				#指定自定义网络
  blog:
```

在启动前，将上次创建的容器、卷、自定义网络全部删除

![image-20240710210314603](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710210314603.png)![image-20240710210349934](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710210349934.png)

一次性启动所有容器

```
docker compose -f compose.yaml up -d
```

-f 指定启动的文件，如果不指定默认以compose.yaml文件启动 

-d 后台启动



![image-20240710212301437](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710212301437.png)

注意：所创建的卷名，自定义网络名不是单纯的卷名，网络名，而是应用名_卷名等，如在这个例中中，yarl文件中  `name: myblog`，加上 

```
networks:          #加入到自定义网络，该网络也需要在最外层指定
      - blog
```

故自定义网络名为`myblog_blog`

##### 移除创建的镜像

![image-20240710212839879](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710212839879.png)

## **Dockerfile**

将自己的jar包打包成镜像（好多配置）

```sh
FROM openjdk:17

LABEL author=leifengyang

COPY app.jar /app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","/app.jar"]
```

## [ 一键安装超多中间件](https://www.yuque.com/leifengyang/sutong/au0lv3sv3eldsmn8#apfCS)

![image-20240710214316081](docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20240710214316081.png)

来自尚硅谷

```yaml
#Disable memory paging and swapping performance
sudo swapoff -a

# Edit the sysctl config file
sudo vi /etc/sysctl.conf

# Add a line to define the desired value
# or change the value if the key exists,
# and then save your changes.
vm.max_map_count=262144

# Reload the kernel parameters using sysctl
sudo sysctl -p

# Verify that the change was applied by checking the value
cat /proc/sys/vm/max_map_count
```

注意：
●将下面文件中 kafka 的  119.45.147.122 改为你自己的服务器IP。
●所有容器都做了时间同步，这样容器的时间和linux主机的时间就一致了
准备一个 compose.yaml文件，内容如下：

```yaml
name: devsoft
services:
  redis:
    image: bitnami/redis:latest
    restart: always
    container_name: redis
    environment:
      - REDIS_PASSWORD=123456
    ports:
      - '6379:6379'
    volumes:
      - redis-data:/bitnami/redis/data
      - redis-conf:/opt/bitnami/redis/mounted-etc
      - /etc/localtime:/etc/localtime:ro

  mysql:
    image: mysql:8.0.31
    restart: always
    container_name: mysql
    environment:
      - MYSQL_ROOT_PASSWORD=123456
    ports:
      - '3306:3306'
      - '33060:33060'
    volumes:
      - mysql-conf:/etc/mysql/conf.d
      - mysql-data:/var/lib/mysql
      - /etc/localtime:/etc/localtime:ro

  rabbit:
    image: rabbitmq:3-management
    restart: always
    container_name: rabbitmq
    ports:
      - "5672:5672"
      - "15672:15672"
    environment:
      - RABBITMQ_DEFAULT_USER=rabbit
      - RABBITMQ_DEFAULT_PASS=rabbit
      - RABBITMQ_DEFAULT_VHOST=dev
    volumes:
      - rabbit-data:/var/lib/rabbitmq
      - rabbit-app:/etc/rabbitmq
      - /etc/localtime:/etc/localtime:ro
  opensearch-node1:
    image: opensearchproject/opensearch:2.13.0
    container_name: opensearch-node1
    environment:
      - cluster.name=opensearch-cluster # Name the cluster
      - node.name=opensearch-node1 # Name the node that will run in this container
      - discovery.seed_hosts=opensearch-node1,opensearch-node2 # Nodes to look for when discovering the cluster
      - cluster.initial_cluster_manager_nodes=opensearch-node1,opensearch-node2 # Nodes eligibile to serve as cluster manager
      - bootstrap.memory_lock=true # Disable JVM heap memory swapping
      - "OPENSEARCH_JAVA_OPTS=-Xms512m -Xmx512m" # Set min and max JVM heap sizes to at least 50% of system RAM
      - "DISABLE_INSTALL_DEMO_CONFIG=true" # Prevents execution of bundled demo script which installs demo certificates and security configurations to OpenSearch
      - "DISABLE_SECURITY_PLUGIN=true" # Disables Security plugin
    ulimits:
      memlock:
        soft: -1 # Set memlock to unlimited (no soft or hard limit)
        hard: -1
      nofile:
        soft: 65536 # Maximum number of open files for the opensearch user - set to at least 65536
        hard: 65536
    volumes:
      - opensearch-data1:/usr/share/opensearch/data # Creates volume called opensearch-data1 and mounts it to the container
      - /etc/localtime:/etc/localtime:ro
    ports:
      - 9200:9200 # REST API
      - 9600:9600 # Performance Analyzer

  opensearch-node2:
    image: opensearchproject/opensearch:2.13.0
    container_name: opensearch-node2
    environment:
      - cluster.name=opensearch-cluster # Name the cluster
      - node.name=opensearch-node2 # Name the node that will run in this container
      - discovery.seed_hosts=opensearch-node1,opensearch-node2 # Nodes to look for when discovering the cluster
      - cluster.initial_cluster_manager_nodes=opensearch-node1,opensearch-node2 # Nodes eligibile to serve as cluster manager
      - bootstrap.memory_lock=true # Disable JVM heap memory swapping
      - "OPENSEARCH_JAVA_OPTS=-Xms512m -Xmx512m" # Set min and max JVM heap sizes to at least 50% of system RAM
      - "DISABLE_INSTALL_DEMO_CONFIG=true" # Prevents execution of bundled demo script which installs demo certificates and security configurations to OpenSearch
      - "DISABLE_SECURITY_PLUGIN=true" # Disables Security plugin
    ulimits:
      memlock:
        soft: -1 # Set memlock to unlimited (no soft or hard limit)
        hard: -1
      nofile:
        soft: 65536 # Maximum number of open files for the opensearch user - set to at least 65536
        hard: 65536
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - opensearch-data2:/usr/share/opensearch/data # Creates volume called opensearch-data2 and mounts it to the container

  opensearch-dashboards:
    image: opensearchproject/opensearch-dashboards:2.13.0
    container_name: opensearch-dashboards
    ports:
      - 5601:5601 # Map host port 5601 to container port 5601
    expose:
      - "5601" # Expose port 5601 for web access to OpenSearch Dashboards
    environment:
      - 'OPENSEARCH_HOSTS=["http://opensearch-node1:9200","http://opensearch-node2:9200"]'
      - "DISABLE_SECURITY_DASHBOARDS_PLUGIN=true" # disables security dashboards plugin in OpenSearch Dashboards
    volumes:
      - /etc/localtime:/etc/localtime:ro
  zookeeper:
    image: bitnami/zookeeper:3.9
    container_name: zookeeper
    restart: always
    ports:
      - "2181:2181"
    volumes:
      - "zookeeper_data:/bitnami"
      - /etc/localtime:/etc/localtime:ro
    environment:
      - ALLOW_ANONYMOUS_LOGIN=yes

  kafka:
    image: 'bitnami/kafka:3.4'
    container_name: kafka
    restart: always
    hostname: kafka
    ports:
      - '9092:9092'
      - '9094:9094'
    environment:
      - KAFKA_CFG_NODE_ID=0
      - KAFKA_CFG_PROCESS_ROLES=controller,broker
      - KAFKA_CFG_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093,EXTERNAL://0.0.0.0:9094
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://kafka:9092,EXTERNAL://119.45.147.122:9094
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,EXTERNAL:PLAINTEXT,PLAINTEXT:PLAINTEXT
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=0@kafka:9093
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - ALLOW_PLAINTEXT_LISTENER=yes
      - "KAFKA_HEAP_OPTS=-Xmx512m -Xms512m"
    volumes:
      - kafka-conf:/bitnami/kafka/config
      - kafka-data:/bitnami/kafka/data
      - /etc/localtime:/etc/localtime:ro
  kafka-ui:
    container_name: kafka-ui
    image: provectuslabs/kafka-ui:latest
    restart: always
    ports:
      - 8080:8080
    environment:
      DYNAMIC_CONFIG_ENABLED: true
      KAFKA_CLUSTERS_0_NAME: kafka-dev
      KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka:9092
    volumes:
      - kafkaui-app:/etc/kafkaui
      - /etc/localtime:/etc/localtime:ro

  nacos:
    image: nacos/nacos-server:v2.3.1
    container_name: nacos
    ports:
      - 8848:8848
      - 9848:9848
    environment:
      - PREFER_HOST_MODE=hostname
      - MODE=standalone
      - JVM_XMX=512m
      - JVM_XMS=512m
      - SPRING_DATASOURCE_PLATFORM=mysql
      - MYSQL_SERVICE_HOST=nacos-mysql
      - MYSQL_SERVICE_DB_NAME=nacos_devtest
      - MYSQL_SERVICE_PORT=3306
      - MYSQL_SERVICE_USER=nacos
      - MYSQL_SERVICE_PASSWORD=nacos
      - MYSQL_SERVICE_DB_PARAM=characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true
      - NACOS_AUTH_IDENTITY_KEY=2222
      - NACOS_AUTH_IDENTITY_VALUE=2xxx
      - NACOS_AUTH_TOKEN=SecretKey012345678901234567890123456789012345678901234567890123456789
      - NACOS_AUTH_ENABLE=true
    volumes:
      - /app/nacos/standalone-logs/:/home/nacos/logs
      - /etc/localtime:/etc/localtime:ro
    depends_on:
      nacos-mysql:
        condition: service_healthy
  nacos-mysql:
    container_name: nacos-mysql
    build:
      context: .
      dockerfile_inline: |
        FROM mysql:8.0.31
        ADD https://raw.githubusercontent.com/alibaba/nacos/2.3.2/distribution/conf/mysql-schema.sql /docker-entrypoint-initdb.d/nacos-mysql.sql
        RUN chown -R mysql:mysql /docker-entrypoint-initdb.d/nacos-mysql.sql
        EXPOSE 3306
        CMD ["mysqld", "--character-set-server=utf8mb4", "--collation-server=utf8mb4_unicode_ci"]
    image: nacos/mysql:8.0.30
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=nacos_devtest
      - MYSQL_USER=nacos
      - MYSQL_PASSWORD=nacos
      - LANG=C.UTF-8
    volumes:
      - nacos-mysqldata:/var/lib/mysql
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "13306:3306"
    healthcheck:
      test: [ "CMD", "mysqladmin" ,"ping", "-h", "localhost" ]
      interval: 5s
      timeout: 10s
      retries: 10
  prometheus:
    image: prom/prometheus:v2.52.0
    container_name: prometheus
    restart: always
    ports:
      - 9090:9090
    volumes:
      - prometheus-data:/prometheus
      - prometheus-conf:/etc/prometheus
      - /etc/localtime:/etc/localtime:ro

  grafana:
    image: grafana/grafana:10.4.2
    container_name: grafana
    restart: always
    ports:
      - 3000:3000
    volumes:
      - grafana-data:/var/lib/grafana
      - /etc/localtime:/etc/localtime:ro

volumes:
  redis-data:
  redis-conf:
  mysql-conf:
  mysql-data:
  rabbit-data:
  rabbit-app:
  opensearch-data1:
  opensearch-data2:
  nacos-mysqldata:
  zookeeper_data:
  kafka-conf:
  kafka-data:
  kafkaui-app:
  prometheus-data:
  prometheus-conf:
  grafana-data:
```

