---
layout: post
categories: 操作系统和服务
tags: [虚拟机]
---

有同学因为课程要求，需要在`Windows`中安装`VMware`虚拟机，过程中遇到了一些问题。
这里分享一下我的安装配置经验。

## 一、 安装VMware Player

1. 安装前的准备

    如果你的`Windows`启用了`WSL`，那么请将其升级到`WSL2`。

2. 下载安装

    从官网下载`VMware Player 16`的合适版本并安装。

    安装过程中，请勾选**安装 Windows Hyper-V Platform(WHP)**，这可以保证`WSL`和`Player`可以共存。

    安装完成后`Windows`会重启以使设置生效。第一次启动`Player`时，可能会提示有更新，请选择更新。

    >Player用于非商业用途是免费的，不需要许可证。

## 二、 安装Ubuntu

这里以安装`Ubuntu`为例。

1. 下载镜像
    
    1). 从清华大学`https://mirror.tuna.tsinghua.edu.cn/ubuntu-releases`网站，下载`Ubuntu XXX`桌面版镜像（`ubuntu-XXX-desktop-amd64.iso`），把它存放到（例如）`D:\iso`目录中。
    >注：XXX是Ubuntu的版本号。建议选择最高版本。

    2). 打开`https://mirror.tuna.tsinghua.edu.cn/help/ubuntu/`页面，选择版本 XXX，将选项下面文本框中的`sources.list`样本复制下来，然后在`Windows`中打开`Notepad`，将内容粘贴到其中，并保存为`D:\iso\sources.list`。
    >这个文件的作用是，系统的更新源指向清华tuna镜像站点，这可以使更新下载更快。

2. 安装Ubuntu
    
    打开`Player`，在**主页**中选择**创建虚拟机**。
    
    1). 在弹出的窗口中，选择**安装程序光盘映像文件(iso)**，按**浏览**，找到`D:\iso\ubuntu-XXX-desktop-amd64.iso`，选择**打开**。

    2). 此后的页面就是一系列设置过程。请根据实际情况填写相关内容。
    >强烈建议不要安装到C:盘

    3). 此后就是`Ubuntu`的安装过程。请根据实际情况设置相关内容。

## 三、 配置Player

1. 软件更新

    点击`Player`左上角的**Player**旁的向下箭头，选择**文件**->**首选项**，在弹出的窗口中勾选**软件更新**中的两项。

2. VMware Tools

    选中已安装的镜像，然后点击`Player`左上角的**Player**旁的向下箭头，选择**管理**->**虚拟机设置**，在弹出的窗口中上方，选择**选项**卡，点击**VMware Tools**，勾选**时间同步**和**自动更新**两项。


## 四、 配置Ubuntu

1. 选中已安装的镜像并启动它。

2. 安装`open-vm-tools`
    打开终端，发出如下命令：
    ```bash
    sudo apt install open-vm-tools open-vm-tools-desktop
    ```
    如果顺利安装，那么主机系统和虚拟机之间可以实现文件的复制、粘贴和拖放。
    此后可以跳过**第3步**。

3. 安装/更新`VMware Tools`
    
    如果你的虚拟机不能成功安装`open-vm-tools`，那么需手动安装`VMware Tools`。

    在`Player`界面的左上角点击一下，展开**管理**菜单。如果**安装/更新VMware Tools**或者**重新安装VMware Tools**可以用（不能用时是灰色的），那么点击它。此后`Ubuntu`左侧栏会出现一个光盘图标。然后按如下步骤安装：

    - 查看光盘设备是否挂载
        ```bash
        mount | grep cdrom
        ```
        如果显示已经挂载，则跳到**解压**步骤；如果没有，则发出如下指令：
        ```bash
        cd /mnt
        sudo mkdir -p cdrom
        sudo mount /dev/cdrom /mnt/cdrom
        ```
    
    - 解压`VMware Tools`tar包
        ```bash
        cd /tmp
        tar zxpf /mnt/cdrom/VMwareTools-x.x.x-yyyy.tar.gz
        ```
        >x、y等是实际的版本号，请根据实际情况替换。但你不必敲完tar包的名字，只需键入前几个字母，然后按tab键，系统会自动补全。

    - 卸载光盘设备
        ```bash
        umount /dev/cdrom 
        ```

    - 安装
        ```bash
        cd vmware-tools-distrib
        sudo ./vmware-install.pl
        ```
        如果适合你的配置，请按照提示接受默认值。
        按照脚本结尾处的说明进行操作。

4. 配置更新源

    1). 在`Ubuntu`桌面上打开`Home`文件夹， 再打开`Downloads`文件夹。将`D:\iso\sources.list`复制粘贴到里面。

    2). 打开终端，发出如下一系列命令：
    ```bash
    cd /etc/apt
    sudo mv sources.list sources.list.bak
    sudo mv ~/Downloads/source.list .
    sudo apt update
    sudo apt dist-upgrade -y
    sudo apt autoremove
    ```

---