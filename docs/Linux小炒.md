# Linux小炒.md

在类 UNIX 系统中，路径是以正斜杠 `/` 分隔的，也不采用 Windows 下的盘符机制，而是采用统一的根目录。

```
./file.txt  # 当前目录下的 file.txt 文件
../file.txt # 上一级目录（父目录）下的 file.txt 文件
../abc/file.txt # 上一级目录（父目录）下的 abc 文件夹下的 file.txt 文件
```



## 搜索软件包

下面是 `apt search firefox` 搜索火狐浏览器的输出结果示例，由于输出结果过多，去除了无用的其他软件包：

```
$ apt search firefox
Sorting... Done
Full Text Search... Done
(Output omitted)

firefox/bionic-updates,bionic-security,now 72.0.2+build1-0ubuntu0.18.04.1 amd64
  Safe and easy web browser from Mozilala

(Output omitted)
```

中间两行每个字段的含义：

| 样例中的字段                             | 含义                                  |
| :--------------------------------------- | :------------------------------------ |
| `firefox`                                | 即为在软件仓库中的包名                |
| `bionic-updates,bionic-security,now`     | 为包含这个软件包的仓库源              |
| `72.0.2+build1-0ubuntu0.18.04.1`         | 为软件包的版本                        |
| `amd64`                                  | 软件包的架构；还可能为`i386`、`all`等 |
| `Safe and easy web browser from Mozilla` | 在软件仓库中对这个软件包的描述        |