
## 1.先确认本地环境是什么？

```
1. 通过ostnamectl命令可以提供系统的详细信息，包括操作系统、内核、架构等：
   hostnamectl


2. 检查 which 命令是否存在；如这里就是查的mysql；返回的就是mysql的路径。
	which mysql

```



## 2.安装项目需要的数据库

### 2.1安装Redis数据库

### 2.1.1第一步：安装 Redis
由于你的系统可能默认源中的 Redis 版本较旧，为了获得更好的兼容性，建议先安装 EPEL 仓库（Extra Packages for Enterprise Linux），或者使用 Remi 仓库。
执行以下命令：
```
1. 安装 EPEL 仓库（包含 Redis 包） 
   
   yum install epel-release -y 
   
 2. 安装 Redis yum install redis -y
   
```
`
### 第二步：启动并设置开机自启
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

### 第三步：验证 Redis 是否正常工作

为了确保 Java 程序能连上，我们可以简单测试一下：

```
# 进入 Redis 命令行客户端 redis-cli # 在出现的 `127.0.0.1:6379>` 提示符下输入： ping
```






先安装
### 直接使用 yum 安装 wget

```
# 最简单的安装方式
yum install -y wget

# 验证安装
wget --version
```


常用指令
检查 

# 检查是否已安装(这里是以wget)
rpm -qa | grep wget 

# 检查 wget 命令是否存在
which wget



安装 jdk
## 方法一：使用 RPM 包安装（推荐）

### 1. 下载 JDK 21 RPM 包

```
# 进入下载目录
cd /usr/local/src

# 下载 JDK 21 RPM 包（选择适合的版本）
wget https://download.oracle.com/java/21/latest/jdk-21_linux-x64_bin.rpm

# 或者使用 OpenJDK
wget https://download.java.net/java/GA/jdk21.0.3/6b9c3db954894a4fbcc32f137aefb50f/9/GPL/openjdk-21.0.3_linux-x64_bin.rpm
```

### 2. 安装 JDK 21

```
# 安装 RPM 包
sudo rpm -ivh jdk-21_linux-x64_bin.rpm

# 或者使用 yum 安装
sudo yum localinstall jdk-21_linux-x64_bin.rpm
```

### 3. 验证安装

```
# 检查 Java 版本
java -version

# 检查 javac 版本
javac -version
```



### 查看软件包的详细信息

```
# 快速检查是否安装
rpm -q 软件名

# 查看详细信息
rpm -qi 软件名

# 搜索相关软件
rpm -qa | grep 关键词
```

例如检查 wget：

```
rpm -q wget
```

如果显示版本号说明已安装，如果显示 "package wget is not installed" 说明未安装。



## 配置 JDK 环境变量

### 1. 首先找到 JDK 的安装路径

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

### 2. 配置环境变量

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

## 快速配置脚本

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

```
# 重新加载环境变量
source /etc/profile

# 或者退出重新登录
logout
```

### 4. 验证配置

```
# 检查环境变量
echo $JAVA_HOME
echo $PATH

# 验证 Java 版本
java -version
javac -version

# 测试编译和运行
echo 'public class Test { public static void main(String[] args) { System.out.println("JDK配置成功!"); } }' > Test.java
javac Test.java
java Test
```



## 使用 alternatives 配置默认 Java（如果有多个版本）

```
# 查看所有 Java 版本
alternatives --config java

# 设置默认 Java 版本
alternatives --set java /usr/lib/jvm/java-21-openjdk-21.0.3.0.7-1.el7_9.x86_64/bin/java
alternatives --set javac /usr/lib/jvm/java-21-openjdk-21.0.3.0.7-1.el7_9.x86_64/bi
```


## 部署 Java 项目的基本步骤

### 1. 准备项目文件

```
# 创建项目目录
mkdir -p /opt/myjavaapp
cd /opt/myjavaapp

# 上传您的 jar 文件或 war 文件

# mv [选项] 源文件或目录 目标文件或目录

```

### 2. 运行 Java 项目

```
# 运行 jar 包
java -jar your-application.jar

# 或者指定配置文件
java -jar your-application.jar --spring.config.location=application.properties

# 后台运行
nohup java -jar your-application.jar > app.log 2>&1 &
```

### 3. 检查进程

```
# 查看 Java 进程
ps -ef | grep java

# 查看端口占用
netstat -tlnp | grep java
`
```

## 常见问题解决

如果遇到权限问题：

```
# 给执行权限
chmod +x your-application.jar

# 如果端口被占用，使用其他端口
java -jar your-application.jar --server.port=8081




nohup java -jar /home/project/ruoyi/ruoyi-admin.jar --server.port=8081 > app.log 2>&1 &