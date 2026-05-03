
## 1.先确认本地环境是什么？

```
1. 通过ostnamectl命令可以提供系统的详细信息，包括操作系统、内核、架构等：
   hostnamectl


2. 检查 which 命令是否存在；如这里就是查的mysql；返回的就是mysql的路径。
	which mysql

```


## 2 确认项目需要那些软件
### 2.1 安装 Redis

#### 2.1.1第一步：安装 Redis
由于你的系统可能默认源中的 Redis 版本较旧，为了获得更好的兼容性，建议先安装 EPEL 仓库（Extra Packages for Enterprise Linux），或者使用 Remi 仓库。
执行以下命令：
```
1. 安装 EPEL 仓库（包含 Redis 包） 
   
   yum install epel-release -y 
   
 1. 安装 Redis yum install redis -y
```
`
#### 2.1.2第二步：启动并设置开机自启
安装完成后，我们需要启动 Redis，并设置它随系统启动自动运行。
**执行以下命令：**

```
# 1. 启动 Redis 服务 
systemctl start redis 
# 2. 设置开机自启 
systemctl enable redis 
# 3. 查看状态（验证是否成功） 
systemctl status redis
```

#### 2.1.3 第三步：验证 Redis 是否正常工作

为了确保 Java 程序能连上，我们可以简单测试一下：

```
# 进入 Redis 命令行客户端 redis-cli # 在出现的 `127.0.0.1:6379>` 提示符下输入： ping

```
#### 2.1.4修改配置文件

1.打开配置文件
通常 Redis 的配置文件位于 /etc/redis.conf。

vi /etc/redis.conf

2.在编辑器中按 / 键，输入 requirepass，然后按回车查找。
你会看到一行被注释掉的代码：# requirepass foobared
置你的密码去掉行首的 # 号（取消注释）。将 foobared 修改为你想要设置的密码。
示例（假设密码设为 admin）：

requirepass    admin


3.保存并退出
按 Esc 键，输入 :wq 保存并退出。如果误操作就输入:q! 
为了让配置生效，必须重启 Redis。在终端执行：

systemctl restart redis

### 2.2 安装 Msql

#### 1. 检查是否已经安装了 mysql

```
检查是否已安装(这里是以wget)
rpm -qa | grep mysql 

检查 wget 命令是否存在
which mysql
```

#### 2.配置阿里云镜像源并安装依赖
由于官方仓库已失效，我们先替换为阿里云的镜像源，并安装必要的工具：

```
# 1. 备份原有 yum 源并下载阿里云源 

mv /etc/yum.repos.d /etc/yum.repos.d.bak 
mkdir /etc/yum.repos.d 
wget -O /etc/yum.repos.d/CentOS-Base.repo 
https://mirrors.aliyun.com/repo/Centos-7.repo 

# 2. 清理并重建 yum 缓存 

yum clean all 
yum makecache 

# 3. 安装常用依赖工具 

yum install -y wget libaio perl net-tools

```

#### 3.添加 MySQL 官方 YUM 仓库并安装

这里以安装稳定且常用的 MySQL 5.7 版本为例：

```
# 1. 下载并安装 MySQL 官方的 YUM 仓库配置包（这里指定 5.7 版本） 

rpm -Uvh https://repo.mysql.com/mysql57-community-release-el7-11.noarch.rpm 

# 2. 导入官方 GPG 密钥（避免安装时出现签名报错） 

rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022 

# 3. 使用 yum 安装 MySQL 服务端 

yum install -y mysql-community-server
```
#### 4.启动 MySQL 并获取初始密码


```
# 1. 启动 MySQL 服务，并设置开机自启 

systemctl start mysqld systemctl enable mysqld 

# 2. 查看 MySQL 自动生成的临时密码（冒号后面的就是密码，记得复制保存） 

grep 'temporary password' /var/log/mysqld.log

```

#### 5.登录并修改密码
MySQL 5.7 默认开启了密码强度校验，新密码必须包含大小写字母、数字和特殊符号（例如：Mysql@1234）。
```
# 1. 使用临时密码登录 

MySQL mysql -u root -p 
# （输入刚才查到的临时密码后回车） 

# 2. 在 mysql> 提示符下，修改 root 密码（将 '你的新密码' 替换为你设置的强密码） 

ALTER USER 'root'@'localhost' IDENTIFIED BY '你的新密码'; 

# 3. 刷新权限并退出 

FLUSH PRIVILEGES; exit;

```

####  6. 开放防火墙端口（可选）
如果你需要从本地电脑或其他服务器远程连接这个数据库，需要开放 3306 端口：

```
firewall-cmd --add-port=3306/tcp --permanent 
firewall-cmd --reload
```


#### 7.数据库导入数据

#### 方法一：导入 SQL 文件（最常用）

```
如果你有一个 .sql 后缀的备份文件（例如 backup.sql），这是最标准的方法。数据库名称在没有导入之前就存在命令格式如下：

mysql -u [用户名] -p [数据库名] < [文件路径]

```



### 2.3 安装 JDK

#### 1 检查是否已经 安装

```
# 检查是否已安装
rpm -qa | grep java 

# 检查 java 命令是否存在
which java
```

#### 2. 安装 JDK 21

```
# 安装 RPM 包
sudo rpm -ivh jdk-21_linux-x64_bin.rpm

# 或者使用 yum 安装
sudo yum localinstall jdk-21_linux-x64_bin.rpm
```

#### 3. 验证安装

```
# 检查 Java 版本
java -version

# 检查 javac 版本
javac -version
```

####  4配置 JDK 环境变量

#####  4.1 首先找到 JDK 的安装路径

```
# 查找 Java 安装位置
which java
# 输出：/usr/bin/java

# 查看真实路径（通常是个符号链接）
ls -l /usr/bin/java
# 输出：/usr/bin/java -> /etc/alternatives/java

ls -l /etc/alternatives/java
# 输出真实的 JDK 路径，如：/usr/lib/jvm/java-21-openjdk-21.0.3.0.7-1.el7_9.x86_64/bin/java

# 或者直接查看 JDK 目录
ls /usr/lib/jvm/
```

##### 4.2 配置环境变量

编辑环境变量配置文件：

```
# 使用 vi 编辑器
vi /etc/profile
按 i  就开始编辑
- **保存并退出**：输入 `:wq`然后按 **回车键**（适用于你修改了内容想保存的情况）。
    
- **不保存强制退出**：输入 `:q!`然后按 **回车键**（适用于你没修改内容，或者误打开想直接关闭的情况）。

# 或者使用 nano（如果已安装）
nano /etc/profile
```

在文件末尾添加以下内容：

```
# JDK 环境配置
export JAVA_HOME=/usr/lib/jvm/java-21-openjdk-21.0.3.0.7-1.el7_9.x86_64  # 根据实际路径修改
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
export PATH=$JAVA_HOME/bin:$PATH
```

注意：请将 `JAVA_HOME` 路径替换为您实际的 JDK 安装路径。

##### 4.3快速配置脚本

您可以创建一个自动配置脚本：

```
#!/bin/bash
# auto_config_java.sh

# 查找 JDK 路径
JAVA_PATH=$(ls -d /usr/lib/jvm/java-* | head -1)

# 备份原有配置
cp /etc/profile /etc/profile.bak

# 添加环境变量
echo "export JAVA_HOME=$JAVA_PATH" >> /etc/profile
echo "export PATH=\$JAVA_HOME/bin:\$PATH" >> /etc/profile
echo "export CLASSPATH=.:\$JAVA_HOME/lib" >> /etc/profile

# 生效配置
source /etc/profile

# 验证配置
echo "JAVA_HOME: $JAVA_HOME"
java -version

### 3. 使配置生效

重新加载环境变量
source /etc/profile

或者退出重新登录

logout



# 检查环境变量
echo $JAVA_HOME
echo $PATH

# 验证 Java 版本
java -version
javac -version
```


#####  4.4 运行 Java 项目

```
# 运行 jar 包
java -jar your-application.jar

# 或者指定配置文件
java -jar your-application.jar --spring.config.location=application.properties

# 后台运行
nohup java -jar your-application.jar > app.log 2>&1 &
```

##### 4.5检查进程

```
# 查看 Java 进程
ps -ef | grep java

# 查看端口占用
netstat -tlnp | grep java

```