---
layout: post
categories: 操作系统和服务
tags: [虚拟机,Ubuntu,MySQL]
---

## 一、安装
在`Ubuntu`终端中运行如下命令：
```bash
sudo apt install mysql-server mysql-client libmysqlclient-dev
```

如果安装完成后，`MySQL`服务启动有问题，那么请试试这样做。
```bash
sudo service mysql stop
cd /var/run
sudo mkdir -p mysqld
sudo chown mysql mysqld
sudo usermod -d /var/lib/mysql/ mysql
sudo service mysql restart
```

>根据我的经验，以上错误最有可能发生在使用WSL中的Ubuntu作为平台时。在Ubuntu Server下，安装过程非常平滑，没有类似问题。

## 二、配置

1. 修改配置
    ```bash
    sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
    ```
    在编辑器中修改如下两项：
    ```ini
    bind-address = 0.0.0.0
    mysqlx-bind-address = 0.0.0.0
    ```

2. 安全设置
    运行如下命令：
    ```bash
    mysql_secure_installation
    ```
    然后系统会提出问题，请你选择：
    - Q1: VALIDATE...，是否启用严格密码机制，回答`n`。这样虽然不太安全，它可以是密码没那么复杂。
    - Q2: Password(for root)...，询问是否为`root`设置密码。回答`y`。请按提示设置密码。需要输入两次以验证。
    - Q3: Anonymous user...，询问是否删除匿名用户。回答`n`。
    - Q4: Root from localhost...，询问是否禁止`root`远程登录。回答`y`。
    - Q5: test database...，询问是否删除测试数据库`test`。回答`n`。
    - Q6: Reload privilege...，询问是否刷新权限表，回答`y`。

3. 重启服务
    ```bash
    sudo service mysql restart
    ```

## 三、数据库操作
1. 添加用户
    
    假设你要添加用户'abc'，并设置密码'123456'。
    首先登录`mysql`。
    ```bash
    sudo mysql -u root -p
    ```
    >住：用root用户登录需要sudo。

    在`mysql`命令界面中发出如下命令：
    ```SQL
    use mysql;
    create user 'abc'@'%' identified by '123456';
    grant all on *.* to 'abc'@'%' with grant option;
    exit
    ```
    
    其中的`grant`命令将在所有用户自建数据库上的所有操作权限赋予了用户'abc’。

    >上例中的grant命令给了用户abc极大的权限。在实际应用中，应该根据需要授予合适的权限。

2. 用户操作
    
    首先用'abc'用户登录：
    ```bash
    mysql -u abc -p
    ```

    在`mysql`命令界面中发出如下命令创建数据库：
    ```SQL
    create database mydb;
    use mydb;
    create table mytable(...);
    ```

## 四、PHP设置
假设使用`PDO`方式连接数据库。

1. 安装`PHP`扩展
    ```bash
    sudo apt install php8.1-pdo-mysql php8.1-pdo-odbc
    ```

2. 修改`php.ini`

    打开`/etc/php/8.1/fpm/php.ini`，然后找到`extensions`这一节，打开这些扩展的注释（即删掉前面的;）：
    - mbstring
    - exif
    - pdo-mysql
    - pdo-odbc

3. 重启`php8.1-fpm`服务
    ```bash
    sudo service php8.1-fpm restart
    ```

4. `PHP`脚本示例
    ```PHP
    try {
        $db = new PDO("mysql:dbname=mydb;host=localhost", 'abc', '123456',
        [   PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,        //出错时抛出异常
            PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,   //结果集使用关联数组
            PDO::ATTR_EMULATE_PREPARES => true,                 //模拟prepare，防止SQL Injection
            PDO::ATTR_ORACLE_NULLS => PDO::NULL_EMPTY_STRING    //原始空串转换为null
        ]);
    } catch (PDOException $e) {
        die( "$e\n");
    }

    $rows = $db->query("SELECT * FROM mytable");
    ```