> 目前mysql8有一个问题：root用户访问权限默认为localhost，导致navicat等软件连接不上，需要新建用户并设置访问权限为%（所有）
### 一、安装完成配置完环境变量后使用dbeaver等工具连接mysql报错（需要开启root远程登录）

> 报错信息：mysqlAccess denied for user 'root'@'localhost' (using password: YES)

### 二、解决方案一：允许root远程登录
具体操作看网址：
https://blog.csdn.net/liliuqing/article/details/88723409

### 三、解决方案二：重装mysql

> 1.卸载mysql

![image](https://github.com/user-attachments/assets/6e940fda-727f-4876-9d2c-c5d8cc1c0a53)

> 2.安装mysql，双击mysql.msi选择新建用户‘hairun’，host权限为%（所有）

<img width="600" alt="604547c71758827bf3fb95cac5b2082" src="https://github.com/user-attachments/assets/84f1540d-fe2a-402b-9652-e1e1c7cc1536">

>3.问题解决