---
layout: post
title: "Navicat远程连接腾讯云MySQL数据库"
subtitle: 'Navicat'
author: "Hufe"
header-img: "img/post-bg-mysql.jpg"
header-mask: 0.3
tags:
  - MySQL
---

## 原因

默认情况下，mysql帐号不允许从远程登陆，只能在localhost登录，若想远程连接云服务器，需要进行授权。


方法步骤：
## 一、远程登录授权
- 登录
`mysql -u root -p`
- 授予全部权限 给 所有库的所有表 用户名为USER 密码为YOUR PASSWORD 
`mysql> grant all privileges on *.* to 'USER'@'%' identified by 'YOUR PASSWORD' with grant option;`  
'YOUR PASSWORD'为自己数据库登陆密码  
- 创建有权通过SSL访问MySQL服务器的用户  
`GRANT ALL PRIVILEGES ON *.* TO 'SSLUSER'@'%' IDENTIFIED BY 'YOUR PASSWORD' REQUIRE SSL;`  
- 刷新MySQL的系统权限相关表，在不重启MySQL服务的情况下直接生效  
`mysql> flush privileges;`  
- 查看修改权限后的user表 ，有host为%，user为root，表示修改成功。  
`mysql> select host,user from user;`  


命令解释:

- `*.*` 第一个`*`表示库，第二个`*`表示表; `*.*`对全部数据库的全部表授权，`so.ok` 表示只对so这个库中的ok表授权
- `USER` 表示要给哪个用户授权，这个用户可以是存在的用户，也可以是不存在的
- `'%'` 表示允许远程连接的IP地址，`%`代表允许所有IP连接
- `'YOUR PASSWORD'`是设置授权用户的连接密码

## 二、 解绑3306端口绑定的IP地址
- 查看3306端口是否状态  
`$ netstat -an | grep 3306`  
`tcp6        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN`  
- 显示3306端口绑定的IP地址是本地的127.0.0.1，需要解绑  
- 修改Mysql配置文件  
`vim /etc/mysql/mysql.conf.d/mysqld.cnf`  
- 找到`bind-address = 127.0.0.1`，在前面加上`#`， 注释该行  
`#bind-address = 127.0.0.1`  
- 重启Mysql让配置文件生效  
`/etc/init.d/mysql restart`  
- 再看3306端口状态  
`netstat -an | grep 3306`  
显示如下，表明解绑成功  
`tcp6       0      0 :::3306                 :::*                    LISTEN `

## 三、设置腾讯云安全组规则
    入站规则 - 添加规则  
输入如下值  
![title](https://raw.githubusercontent.com/huifeng09/GitNote-Images/master/gitnote/2019/03/29/1553873599388-1553873599392.png)

## 四、登录navicat
在navicat或其他MySQL数据库管理工具登录，即可成功连接云服务器的数据库  
![title](https://raw.githubusercontent.com/huifeng09/GitNote-Images/master/gitnote/2019/03/29/1553873610152-1553873610157.png)   
<br>
> 参考自: https://blog.csdn.net/weixin_41471128/article/details/83189083  

