# 检查是否已安装(这里是以wget)
rpm -qa | grep mysql 

# 检查 wget 命令是否存在
which mysql



连接 mysql
mysql -u root -p


方法一：导入 SQL 文件（最常用）
如果你有一个 .sql 后缀的备份文件（例如 backup.sql），这是最标准的方法。
命令格式：
bash

编辑



mysql -u [用户名] -p [数据库名] < [文件路径]
操作示例：
假设你要把 /root/backup.sql 导入到名为 mydb 的数据库中，使用 root 用户：
确保数据库已存在（如果不存在需先创建）：
sql

编辑



create database mydb;
在 Linux 终端执行导入（注意不是在 mysql> 提示符下，而是在 root@localhost 这种系统提示符下）：
bash

编辑



mysql -u root -p 数据库名 < /root/backup.sql  sql 文件
输入密码后，如果没有报错，即表示导入成功。