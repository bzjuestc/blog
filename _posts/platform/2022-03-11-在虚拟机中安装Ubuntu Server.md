---
layout: post
categories: 操作系统和服务
tags: [虚拟机,Ubuntu]
---

如果有同学需要虚拟机中稳定的`Linux`系统服务，那么建议在`VMware`下安装`Ubuntu Server`。

这里分享一下我在`VMware Player 16`下安装配置`Ubuntu Server`的经验。如有谬误，敬请指正。

## 一、 创建虚拟机

1. 下载镜像

    从清华大学`https://mirror.tuna.tsinghua.edu.cn/buntu-releases`网站，下载`Ubuntu Server 21.10`镜像（`ubuntu-21.10-live-server-amd64.iso`），把它存放到（例如`D:\iso`）目录中。

2. 创建虚拟机

    启动`VMware Player`，选择**创建新虚拟机**，选择**安装程序光盘映像文件(iso)**，点击**浏览**选择刚下载的镜像文件。

    按照`Player`的提示进行设置。在设置的最后一页，选择**自定义硬件**。

    >尽量不要将虚拟机安装在C盘。

3. 自定义硬件

    根据实际情况设置内存、CPU核数等。

    点击**网络适配器**，在右侧的**网络连接**栏目中，选择**自定义：特定虚拟网络**，在其下的下拉列表中选择**VMnet(8)(NAT模式)**，然后关闭设置。

    回到前一页面后，点击**完成**。

    此后进入`Ubuntu Server`安装和配置阶段。

## 二、安装Ubuntu Server

首先等待安装程序跑一段时间，然后出现设置页面。

提示：在几乎所有的设置页面，都是用上下键移动选项（选中项是绿色背景），按回车结束。
有些页面有多选一()、多选多[]的情况，可以将光标移到选项上，按空格选中它（出现*或X）。

1. 语言

    选择默认的**English**。

2. 安装程序升级

    选择**升级**，然后等待这一步完成。

3. 键盘布局

    选择默认布局。

4. 安装类型

    根据实际情况选择全安装还是最小安装。我选择了全安装。

5. 网络连接

    选择默认的**DHCP**。
    
    请记住动态IP地址（例如192.168.184.130）， 以后再来它来配置静态IP地址。

    **插话**
    - 一般地，这个IP地址是一个C类地址，其中前三个是子网号，和在`Windows`中看到的虚拟网卡地址的子网号是一样的。
    - 在`Windows`的`Powershell`中，发出如下命令：
        ```Powershell
        ipconfig
        ```
        可以看到类似的信息：
        ```plaintext
        以太网适配器 VMware Network Adapter VMnet8:

            连接特定的 DNS 后缀 . . . . . . . :
            本地链接 IPv6 地址. . . . . . . . :  xxx...
            IPv4 地址 . . . . . . . . . . . . : 192.168.184.1
            子网掩码  . . . . . . . . . . . . : 255.255.255.0
            默认网关. . . . . . . . . . . . . :
        ```

6. 代理服务器

    选择默认空白。

7. 选择镜像网站

    输入：`https://mirror.tuna.tsinghua.edu.cn/buntu`。

8. 存储配置向导

    选择默认配置。

9. 存储配置

    选择默认，并在出现的提问中，选择**Continue**。

10. 用户设置

    在此页面中，输入你的名字，以及服务器的名字（全部小写字母）。

    然后根据需要输入一个账户名（普通权限），并输入两次密码。

11. SSH安装
    
    选择安装**OpenSSH Server**；下面的导入选择**No**。

12. 选择启动的服务

    选择默认设置。

接下来，就是系统安装了。请等待完成，完成重新启动系统就OK了。

>如此安装的服务器没有图形化桌面，只有命令行终端。

## 三、配置Ubuntu Server
启动`Ubuntu Server`并登录。

### 一) 为root账户创建密码
在终端中发出如下命令：
```bash
sudo passwd root
```
先输入登录账户的密码，然后输入两次`root`的密码。

### 二) 设置固定IP地址
一般地，虚拟机中`Ubuntu`的`IP`地址是通过`DHCP`方式动态分配的，因此每次启动后地址可能不一样。为此，最好为虚拟机设置一个固定的IP地址。

从当前用户**logout**，再以**root**账户登录。
或者在当前账户下，在终端中发出如下命令：
```bash
su - root
```
进入**root**账户。

以下操作都是用`root`账户操作的。

因为`Ubuntu Server 21.10`版本不低于**18.04**，因此需按如下方法进行。

1. 修改`/etc/netplan`目录下的配置文件

    在终端中发出如下命令：
    ```bash
    cd /etc/netplan
    ll
    ```
    查看该目录下一个后缀名为`yaml`的文件，然后用`vim`打开它，原始内容类似于：
    ```yaml
    network:
      ethernets:
        ens33:
          dhcp4: true
    version: 2
    ```
    解释一下：这个配置文件说明在网卡`ens33`上启用了`IPv4`版的`DHCP`。

    >注：yaml格式的文件中，同一级别的子项都用相同数量的空格缩进；:后必须有一个空格。

    现将其改为：
    ```yaml
    network:
      ethernets:
        ens33:
          dhcp4: no
          addresses: [192.168.184.130/24]
          routes:
            - to: default
              via: 192.168.184.2
          nameservers:
            addresses: [114.114.114.114, 8.8.8.8]
    version: 2
    ```

    >注：路由一节中，原来的gateway4配置已经被废弃，改为示例中的routes。此配置下必须有to和via两个键值对。

2. 发出如下命令生效：
    ```bash
    netplan apply
    ```

### 三) 设置终端字体、分辨率和颜色

1. 设置字体字号
    
    终端默认的字体是固定点阵字体，比较小，看起来很费劲。因此有必要将字号改大一些。

    在终端中发出命令：
    ```bash
    dpkg-reconfigure console-setup。
    ```
    分别选择**UTF-8**和**Guess ...**，再选择合适的字体，例如我选择了**Terminus**，字号**11x22**。

2. 设置分辨率

    在终端中发出命令：
    ```bash
    vim /etc/default/grub
    ```
    其中有一行：`GRUB_CMDLINE_LINUX_DEFAULT=""`。将其改为：
    ```plaintext
    GRUB_CMDLINE_LINUX_DEFAULT="vga=0x317"
    ```
    这表示选择**1024x768**分辨率。下表是列出了一些参考值。

    |颜色数\分辨率|640x480|800x600|1024x768|1280x1024|
    |:---:|:---:|:---:|:---:|:---:|
    |256|0x301|0x303|0x305|0x307|
    |32k|0x310|0x313|0x316|0x319|
    |64k|0x311|0x314|0x317|0x31A|
    |16M|0x312|0x315|0x318|0x31B|


    >修改grub文件时千万要小心，不要输错了，否则你的系统可能无法正常启动！

    >千万不要使用vga=ask这个设置，因为它已经被废弃了！

    保存退出`vim`后，发出如下命令：
    ```bash
    update-grub
    ```
    下次重启系统后就可以看到效果了。

3. 设置终端提示符颜色

    1). 在终端中发出命令：
    ```bash
    vim /etc/profile
    ```
    在文件末尾添加两行：
    ```plaintext
    export TERMINFO=/usr/share/terminfo
    export TERM=xterm+256color
    ```

    >你可以在/usr/share/terminfo/x目录下查看以x开头的可用的终端信息。

    2). 再发出如下命令：
    ```bash
    cd ~
    vim .bashrc
    ```
    找到**case $TERM**这一行，将其下一行（或类似的）
    ```plaintext
    xterm-color) ...
    ```
    改为：
    ```plaintext
    xterm+256color) ...
    ```
    然后保存退出`vim`。

    3). 接下来发出如下命令：
    ```bash
    vim .bash_profile
    ```
    在文件末尾添加一行：
    ```plaintext
    . ~/.profile
    ```
    然后保存退出`vim`。

    4). 最后发出：
    ```bash
    . .bash_profile
    ```
    就可以看到效果了。

    >如果要为其他用户设置相同效果。那么每个用户都需按上述操作2)-4)进行，编辑自己账户下的.bash_profile、.bashrc。

### 四) 修改时区和日期时间格式

1. 修改时区

    系统默认的时区是世界标准时，因此需要修改了当地时区。
    在终端中发出如下命令：
    ```bash
    tzselect
    ```
    然后根据提示做出选择：Asia、China、Beijing、Yes。输入这些选项对应的数。

    最后，发出如下命令：
    ```bash
    cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
    ```

2. 修改日期时间格式为24小时模式

    ```bash
    vim /etc/default/locale
    ```
    在`LANG`这一行后面添加一行：
    ```plaintext
    LC_TIME=en_DK.UTF-8
    ```
    保存退出。系统重启后就可以看到效果了。

## 四、 远程连接Ubuntu Server

以在`Windows`中使用`Powershell`连接为例。假设要连接到服务器中的`abc`账户。

1. 生成SSH秘钥

    在`Powershell`中发出如下命令：
    ```Powershell
    ssh-keygen -t rsa -C "your-email"
    ```
    在询问保存到哪个文件时，输入你想要的文件名。例如，我输入了`ubuntuserver`。

    此后，上述命令会在`~/.ssh`目录中生成私钥文件`ubuntuserver`和公钥文件`ubuntuserver.pub`。

    >Windows的~目录在哪里？假设你的登录账号时abc，那么这个目录就是：C:\用户\abc

2. 远程复制公钥到`Server`

    在`Powershell`中发出如下命令：
    ```Powershell
    cd ~/.ssh
    scp ubuntuserver.pub abc@192.168.184.130:~/.ssh
    ```
    此命令会要求你输入服务器上用户`abc`的登录密码。

3. 创建/修改`config`文件

    用**记事本**或其他文本编辑器打开`~/.ssh`目录下的`config`（如果存在）。如果不存在，就打开一个空白文件，最后保存成`config`就行。
    
    在文件末尾添加如下几行：
    ```plaintext
    Host ubuntuserver
        HostName 192.168.184.130
        PreferredAuthentications publickey
        IdentityFile ~/.ssh/ubuntuserver
    ```

4. 修改服务器上的`authorzied_keys`

    用账户`abc`登录到服务器，发出如下命令：
    ```bash
    cd ~/.ssh
    cat ubuntuserver.pub >> authorized_keys
    rm ubuntuserver.pub
    ```

5. 测试连接

    在`Powershell`中发出如下命令：
    ```Powershell
    ssh abc@ubuntuserver
    ```
    此后，会看到远程免密登录到服务器的`abc`账户了。

---
