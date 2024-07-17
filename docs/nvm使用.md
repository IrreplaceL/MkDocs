## nvm使用

控制台输入nvm，回车打印消息证明安装完成

![image-20240717152813905](nvm%E4%BD%BF%E7%94%A8.assets/image-20240717152813905.png)

#### **查看安装目录**

```
nvm root
```

![image-20240717152901388](nvm%E4%BD%BF%E7%94%A8.assets/image-20240717152901388.png)

#### **命令行设置淘宝镜像**

```
nvm npm_mirror https://npmmirror.com/mirrors/npm/
nvm node_mirror https://npmmirror.com/mirrors/node/
```

#### **查看nodejs版本**

```
nvm list available
```

![image-20240717153242125](nvm%E4%BD%BF%E7%94%A8.assets/image-20240717153242125.png)

可以查看到最近的版本和LTS长期支持版，如果要查看全部版本则可以到以下网址查看所有发行版本

https://nodejs.org/download/release



#### 安装Node.js

输入以下格式的指令

```bash
nvm install 版本号
```

#### ![image-20240717154250122](nvm%E4%BD%BF%E7%94%A8.assets/image-20240717154250122.png)

#### 使用对应的版本

```bash
nvm use 版本号
```

![image-20240717154320448](nvm%E4%BD%BF%E7%94%A8.assets/image-20240717154320448.png)

#### 查看npm版本

```bash
npm -v
```

![image-20240717154343865](nvm%E4%BD%BF%E7%94%A8.assets/image-20240717154343865.png)

####  npm升级降级安装

```bash
npm install npm@版本号
```

#### 多版本管理

##### 查看下载的Node.js版本列表

```bath
nvm ls
```

Currently using 64-bit executable 表示当前使用的版本

<img src="nvm%E4%BD%BF%E7%94%A8.assets/image-20240717153608939.png" alt="image-20240717153608939" style="zoom:50%;" />

![image-20240717154408988](nvm%E4%BD%BF%E7%94%A8.assets/image-20240717154408988.png)

##### 卸载某个版本

```bash
nvm uninstall 版本号
```



### 安装pnpm

nodejs版本需要16.14以上，推荐18或者20等最新LTS版本

```sh
npm i -g pnpm
```

更新pnpm

```sh
npm i -g pnpm
```

#### 使用nrm管理npm镜像源，加速下载

```sh
npm i -g nrm
```

使用淘宝镜像

```sh
nrm use taoabo
1
```

查看镜像源列表

```sh
nrm ls
```







常见指令

```
nvm-arch #显示节点是以32位还是64位模式运行。

nvm install＜version＞[arch] 
#版本可以是node.js版本，也可以是“最新”的最新稳定版本。
#（可选）指定是安装32位版本还是64位版本（默认为system arch）。
#将[arch]设置为“all”以安装32 AND 64位版本。
#在该命令的末尾添加--unsecurity，以绕过远程下载服务器的SSL验证。

nvm list[可用] # 列出node.js的安装。在末尾键入“available”（可用），查看可以安装的内容。别名为ls。

nvm on #启用node.js版本管理。

nvm off #禁用node.js版本管理。

nvm proxy [url] 
#设置用于下载的代理。将[url]留空以查看当前代理。
#将[url]设置为“none”以删除代理。

nvm node_mirror [url] #设置节点镜像。默认为https://nodejs.org/dist/.将[url]留空以使用默认url。

nvm npm_mirror [url] #设置npm镜像。默认为https://github.com/npm/cli/archive/.将[url]留空为默认url。

nvm uninstall ＜version＞ #版本必须是特定的版本。

nvm use [version] [arch] 
#切换到使用指定的版本。可选择指定32/64位体系结构。
#nvm使用＜arch＞将继续使用所选版本，但切换到32/64位模式。
#nvm-root[path]：设置nvm应存储不同版本node.js的目录。
#如果未设置＜path＞，则会显示当前根目录。

nvm version #显示用于Windows的nvm的当前运行版本。别名为v。
```

