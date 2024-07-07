# 如何创建一个**MkDocs**网站并自动化部署到GitHub上

详细可参考:[https://squidfunk.github.io/mkdocs-material/getting-started/](https://squidfunk.github.io/mkdocs-material/getting-started/)

## 最佳实践

使用python虚拟环境，安装php。

##### 1、在GitHub上新建一个仓库，设为公有

可参考下面设置

<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240707204454915.png" alt="image-20240707204454915" style="zoom:50%;" />

##### 2.将新建的仓库代码clone到本地

利用git bash

`git clone git@github.com:xxxxxxxx`

<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240707204716800.png" alt="image-20240707204716800" style="zoom:50%;" />

进入克隆到本地代码的文件夹，

<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240707204931122.png" alt="image-20240707204931122" style="zoom:50%;" />

##### 3.创建一个python的虚拟环境(确保使用过[`pip`](https://squidfunk.github.io/mkdocs-material/getting-started/#with-pip)Python 包管理器)

- 创建一个名为venv的虚拟环境：

  ```
  python -m venv venv
  ```

  

- 执行命令

  ```
  source venv/Scripts/activate
  ```

  

- 注意进入虚拟环境后，终端会显示（`test_venv`）

- 检查pip是否安装成功：

  ```
  pip --version
  ```

  ![image-20240707212440023](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240707212440023.png)

- 安装所需依赖

  ```
  pip install mkdocs-material
  ```

- 在vscode中打开

  ```
  code .
  ```

- 进入到vscode打开终端

  执行

  ```
  mkdocs new .
  ```

  开启服务

  ```
  mkdocs serve
  ```

  简略部署如下<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240707213148712.png" alt="image-20240707213148712" style="zoom:50%;" />

  最低配置，在`mkdocs.yml`中设置

  ```
  site_name: My site
  site_url: https://mydomain.org/mysite  //目前似乎可忽略，是不是部署后的路径？
  theme:
    name: material
  ```

  

生成相关代码，构建静态站点。代码将存放在site文件夹内	

```
mkdocs build
```



##### 将site文件夹内容推到git仓库就可以啦，然后使用静态一键pages

#### 接下来，如何完成自动部署？

前在前面已经完成了创建仓库并将仓库代码clone到本地的情况下，

在存储库的根目录中，创建一个新的 GitHub Actions 工作流程，路径及文件夹为`.github/workflows/ci.yml`

```
name: ci 
on:
  push:
    branches:
      - master 
      - main
permissions:
  contents: write
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Configure Git Credentials
        run: |
          git config user.name github-actions[bot]
          git config user.email 41898282+github-actions[bot]@users.noreply.github.com
      - uses: actions/setup-python@v5
        with:
          python-version: 3.x
      - run: echo "cache_id=$(date --utc '+%V')" >> $GITHUB_ENV 
      - uses: actions/cache@v4
        with:
          key: mkdocs-material-${{ env.cache_id }}
          path: .cache
          restore-keys: |
            mkdocs-material-
      - run: pip install mkdocs-material 
      - run: mkdocs gh-deploy --force
```

现在，当将新提交推送到`master`或`main`分支时，将自动构建和部署静态站点。推送您的更改以查看实际的工作流程。

##### 如果几分钟后 GitHub Page 没有出现，请转到你的存储库的设置并确保你的 GitHub Page 的[发布源分支](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)设置为`gh-pages`。

您的文档将很快出现在`<username>.github.io/<repository>`。