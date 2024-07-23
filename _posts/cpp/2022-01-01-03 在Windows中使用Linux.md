---
layout: post
categories: C/C++开发环境
tags: [WSL, Linux]
---

## Part 1、启用WSL

**WSL(Windows Subsystrem for Linux)** 是Windows自带的一个很好用的工具平台，可以将其视为是一种轻量级的**虚拟机**。在启用了WSL之后，就能在其下安装某个Linux发行版，例如**Ubuntu**。

本文将详细介绍WSL的安装和配置，以及WSL中Ubuntu的安装。

### 一、 准备工作
1. 检查你的Windows**登录账户名**是否为全英文字母组成的（并且没有空格）。如果是，则直接转到**二、启用WSL**。
因为我们要使用的一些平台有兼容性问题，所以要求Windows的登录账户名必须是全英文。

2. 如果账户名含有中文或其他特殊符号，那么最简单的解决方案就是重新创建一个全英文的Windows账户。
这意味着你在用这个账户登录后，要重新安装必须的软件。

>仅将原有账户改名不能达到目的。所以，你可能需要创建一个新的账户。

### 二、 启用WSL
1. 打开 **控制面板**，选择 **程序和功能**，选择 **启用和关闭Windows功能**，勾选图中红框内的两项：
    <div style="text-align:center;">
    <img src="/assets/images/blog/enable-wsl.png" style="width:40%;">
    </div>
    
2. 重新启动Windows。

#### 3) 升级WSL到WSL2
如果你的`Windows`版本低于**11**，那么你可能要将`WSL`升级到`WSL2`。具体做法如下。

下载`wsl_update_x64.msi`，然后用鼠标双击这个文件进行安装。

>注意：如果安装此文件出现了错误不能安装，那么请忽略这个错误并且不再重试，而是跳过这步，继续后面Part 2中的步骤。

安装好后，**用管理员身份运行**`Powershell`，在窗口中输入如下命令：

```powershell
wsl --set-default-version 2
```

>请注意命令行中，各参数之间有空格。另外，请注意大小写。


## Part 2、在WSL中安装Ubuntu

### 一、搜索Ubuntu
在**启用了WSL**后，打开**Windows Store**，搜索**Ubuntu**，出现如下界面：
<div style="text-align:center;">
<img src="/assets/images/blog/windows-store.png" style="width:70%;">
</div>

### 二、安装Ubuntu 

#### 1) 安装Ubuntu
建议选择顶部第一个的Ubuntu（目前版本是22.04）。选择“获取(get)”，其后的过程就是下载和安装。

Ubuntu安装完成后，你会看到Windows启动了一个“黑框框”，这个是Ubuntu的**终端(terminal)**，你和Ubuntu系统的交互都在这个终端中进行。

>Ubuntu 22.04的安装会有一个类图形化的界面。在这个界面中，需要在指定的输入框中输入信息。

>注：如果终端中提示了错误，多半是因为没有将WSL升级到WSL2。升级后，在Windows的开始菜单中找到Ubuntu，启动它后安装过程将会继续。

#### 2) 创建账户

Ubuntu在第一次运行时，需要你创建一个**账户及其密码**。

1. 创建账户

    当你看到终端中有如下信息（有省略）：
    ```Plaintext
    Enter new UNIX username:
    ```
    此时，你需要在终端中输入一个账户名，以回车结束输入。
    请选一个合适的名字，建议用不长的**全英文，中间没有空格、符号等特殊字符**。注意，英文必须是**小写字母**。

    >Ubuntu 22.04的初始界面与此不同，有操作更方便的输入框。

2. 创建密码

    此后，系统的输出信息是这样的：
    ```bash
    Enter new UNIX password:
    ```
    此时，你需要输入与账户关联的密码。

    >注意！在输入密码时，屏幕上没有回显，即：你在敲键盘时，光标停在原地不动，屏幕上没有显示出你的输入。这是系统的一种安全措施，千万不要认为没有输进去哦！

    >如果是在输入框中输入，那么密码中的每一个字符是用*代替的。

    此后，系统要求再次输入密码以验证正确性。

    **请一定要记住你输入的密码**。

当你看到终端中出现：
```Plaintext
Installation successful!
```
这就表明，你的安装成功了。

**注意** Ubuntu 22.04还会要求配置挂载(mount)选项，如下图所示。在这一页里，不需要额外配置，多按几次Tab键点亮Done，然后按回车就可以了。
<div style="text-align:center;">
<img src="/assets/images/blog/mount-options.png" style="width:70%;">
</div>

#### 3) 关于账户

如果上述操作无误，那么你会在Ubuntu中创建一个**普通账户**，拥有**有限**操作权限。这类账户的标志是：终端命令行提示符的最后一个字符是`$`。相较之下，拥有最高权限的`root`账户的是`#`。

由于普通账户的权限受限，因此在需要更大权限时，你需要在输入的命令前加上`sudo`前缀。例如：
```bash
sudo cp /mnt/e/sources.list /etc/apt
```
注意：第一次使用`sudo`时，需要输入密码。此后，在权限超时之前，不再需要输入密码。
另外，root账户权限最高，因此在输入命令时，**不需要**加上`sudo`。

**如果你在Ubuntu第一次启动时，因为某种原因没有创建普通用户及其密码（例如直接关闭了终端），那么Ubuntu默认的账户就是root，建议你就使用这个账户。以后如果你熟悉了Linux系统，那么你可以自己创建一个普通账户，然后将默认账户改成它。**

一个常见的问题是：如果你忘记了密码，那么很遗憾，你将无法使用`sudo`命令，从而导致系统不能用。这样，你只能重新安装Ubuntu了。

>WSL启动过程中如果出现问题，请自行上网查看解决方案。


## Part 3、 如何将Ubuntu迁移到C盘外的磁盘

在**WSL**中默认安装的Ubuntu位置在Windows的系统盘`C`上面，会占据好几G的空间。为节约空间，我们可以将Ubuntu迁移到其他盘，例如`D`盘。

>请谨慎操作，否则可能破坏你的Ubuntu。

1. 查看Ubuntu的当前用户

    打开Ubuntu终端，命令提示符的形式类似于：
    ```bash
    bzj@xxx …
    ```
    其中，**bzj**就是当前用户。记住这个名字。注意：这是我的账户名，你的肯定和我的不一样。

2. 查看在WSL中注册的Ubuntu的发行版

    以管理员身份运行`Powershell`，发出如下命令：
    ```powershell
    wsl -l
    ```

    输出信息可能是：
    ```plaintext
    适用于 Linux 的 Windows 子系统分发版:
    Ubuntu22.04 (默认)
    ```
    那么系统里安装的Ubuntu发行版就是：**Ubuntu 22.04**。

3. 备份Ubuntu

    在`Powershell`中发出如下命令：
    ```powershell
    wsl --export Ubuntu22.04 d:\ubuntu2204.tar
    ```
    此命令的意思是：将注册的发行版备份到`D`盘根目录下的`ubuntu2204.tar`文件中。

    >注：这条命令还可以用于Ubuntu日常维护中的备份。

4. 注销当前发行版

    在`Powershell`中发出如下命令：
    ```powershell
    wsl --unregister Ubuntu22.04
    ```

5. 新建目录

    在`Powershell`中发出如下命令：
    ```powershell
    d:
    mkdir Ubuntu
    ```
    以上两条命令的功能是：在`D:`盘上创建一个名为`Ubuntu`的子目录（文件夹）。迁移后的`WSL`系统就存放在这个目录中。

6. 导入Ubuntu备份

    在`Powershell`中发出如下命令：
    ```powershell
    wsl --import Ubuntu22.04 d:\Ubuntu d:\ubuntu2204.tar
    ```
    此命令的功能是：将备份在`D:`盘的`ubuntu2204.tar`文件导入到目录`d:\Ubuntu`中，并注册恢复成原来的版本。

    >注：这条命令可以用于恢复以前备份的Ubuntu。

7. 设置用户为以前的用户（否则就默认为root）

    在`Powershell`中发出如下命令：
    ```powershell
    ubuntu2204 config --default-user bzj
    ```

8. 启动Ubuntu

    如果操作无误，那么Ubuntu会正确启动，并且你原来的设置等全部都会保留。

---