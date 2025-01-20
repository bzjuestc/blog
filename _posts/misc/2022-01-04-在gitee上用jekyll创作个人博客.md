---
layout: post
categories: 博客创作
tags: [Gitee, jekyll]
---

本文描述了如何在Gitee平台上，利用Gitee Pages服务开启个人博客的步骤。
实际上，除了博客外，还可以利用平台建立自己的静态网站。

## Part 1、发布平台
发布平台是**码云**`Gitee`。

### 一、申请注册账号
在Gitee平台上实名申请注册账号。假设账号名为**abc**。

### 二、配置账户

#### 1) 本地

建议在本地的`Linux`环境中操作。
打开Linux终端，用`ssh-keygen`创建公私密钥对。例如：
```bash
ssh-keygen -t rsa
```
以上命令会在`~/.ssh`目录下生成秘钥文件`id_rsa`和公钥文件`id_rsa.pub`。

#### 2) Gitee

1. 进入Gitee网站，登录你的账户。
2. 点击右上角的账户图标，选择**设置**，然后在页面的**安全设置**中点击**ssh公钥**。
3. 在右侧页面中，为公钥填入一个**标题**。
4. 在本地用任何编辑软件打开`id_rsa.pub`，然后将其中的所有内容复制到Gitee网页的**公钥**框中，然后**确定**。

此后，你将对名下的仓库拥有所有的控制权，包括拉取**pull**和推送**push**权限。

### 三、创建博客仓库

#### 1) 创建仓库

在平台上创建一个**公用**仓库。这个仓库的名字最好与账号一样，例如**abc**。

>如果不一样，那么博客的网址会有一个子路径，这会使网页中的base路径不是根，需要加上那个额外的子路径。

#### 2) 仓库设置

根据你的设计，对仓库进行一些设置。例如：仓库的属性、许可证类型等等。

#### 3) 添加初始内容

为仓库添加一些初始内容，例如：**README.md**。

### 四、启用Gitee Pages服务

1. 在Gitee页面中点击`abc`仓库，在**服务**中选择**Gitee Pages**。
2. 按照页面的提示要求进行实名认证，等待审批。1个工作日后完成。
    一旦审批通过，那么你的博客网址就是：
    https://abc.gitee.io

>此时访问你的博客会失败，因为还没有内容！

## Part 2、本地创作环境

本地创作平台是`Linux`。

### 一、拉取仓库

假设你的创作环境是`Windows`+`WSL`组合，并准备将远端仓库拉取到`D:`盘根目录下。

在`WSL`终端中发出如下命令：
```bash
cd /mnt/d
git clone git@gitee.com:abc/abc.git
```
其中，**abc**是你的Gitee账户名。
此后，可以在`D:`中看到`abc`目录。你的博客本地原始稿就存放这个目录下。

### 二、安装Ruby

在终端中发出如下命令：
```bash
sudo apt install ruby-full
```

### 三、安装jekyll

在终端中发出如下命令：
```bash
sudo gem install jekyll bundler
```

### 四、初始化本地博客仓库

有两种方式初始化本地博客仓库。

#### 1) 新建
在终端中发出如下命令：
```bash
jekyll new abc
```
此命令会在`abc`目录下生成一些必要的文件和子目录。

#### 2) 使用模板

1. 从`jekyll`相关的网站中，下载你心仪的模板。
2. 将模板压缩包解压后，将其中的所有文件和目录复制到`abc`中。

## Part 3、创作博客

### 一、准备工作

1. 修改依赖文件 **Gemfile**

    根据你的设计，看看需要使用哪些`gem`插件，然后在`abc`目录下的`Gemfile`文件中列出。例如，这是我的博客的`Gemfile`：
    ```ruby
    source "https://rubygems.org"

    gem "kramdown", "~> 2.3.1"
    gem "kramdown-parser-gfm", "~> 1.1.0"
    ```
    这个依赖文件告诉`jekyll`编译器，需要使用**Markdown**到**HTML**的转换器插件。

2. 修改依赖文件 **Gemfile.lock**

    在终端中发出如下命令：
    ```bash
    bundle update
    ```
    此命令将重写`Gemfile.lock`。
    >强烈建议不要手动修改此文件

3. 修改博客全局配置文件 **_config.yml**

    这是一个`YAML`格式的全局配置文件。根据你的设计，编写其中的内容。例如：
    ```yaml
    title: 老白的教学博客
    email: baizj@uestc.edu.cn

    baseurl: ""
    url: ""
    ```
    在此文件中定义的“变量”可以在你的博客脚本中使用。例如：`site.title`。
    >你可以根据需要随时修改此文件。

### 二、创作博客本地稿

1. 编写布局模板
    根据你的设计，编写一系列布局模板（都是**html**文件），并将他们存放到`_layouts`目录下。
    >布局模板一般采用Liquid语法。请参阅相关文档。

2. 编写样式文件
    请参阅`sass`的帮助文档编写博客网页的样式，并将他们存放到`_sass`目录下。

3. 编写**index.md**
    **Pages**服务要求根目录下必须有一个`index.html`文件。因此你需要按照`jekyll`标准编写一个`index.md`文件。它将被`jekyll`编译转换成`index.html`。
    >实际上，你编写的Markdown格式的博客文章都会被这样编译转换。

4. 编写博客本地稿
    请参阅`jekyll`的帮助文档撰写博客文章，并将他们存放到`_posts`目录下并按要求命名。
    根据`jekyll`的要求，每一篇博客都需要一个`YAML`格式的**头信息(Front matter)**。例如：
    ```yaml
    ---
    layout: post
    categories: 编程环境
    tags: [Windows, Linux, VSCode, gcc/g++]
    ---
    ```
    其中：
    - `---`是必须的。
    - `layout`指明这个页面采用`post`布局。
    - `categories`指明文章内容的分类。如果你的布局有侧边栏，那么分类信息会显示在那里。
    - `tags`指明文章内容涉及的标签。如果你的布局按标签分类，那么标签信息会显示在文章标题附近。


5. 编写其他文件

>如果你使用的模板，那么可以阅读修改模板提供的模板、样式以及其他文件。


### 三、本地预览

1. 启动`jekyll`本地服务
    在终端中发出如下命令：
    ```bash
    cd /mnt/d/abc
    jekyll s --no-watch
    ```
    此后，你可以在本地浏览器中，用如下网址访问博客页面：
    http://localhost:4000

2. 编译
    如果博客有更新，那么在终端中发出如下命令：
    ```bash
    cd /mnt/d/abc
    jekyll b
    ```

### 四、发布博客

#### 1) 推送

在终端中发出如下命令将本地仓库推送到远端服务器上：
```bash
cd /mnt/d/abc
git add -A
git commit -m "your commit-message"
git push
```

#### 2) 发布

在**Gitee Pages**页面中，选择**更新**。