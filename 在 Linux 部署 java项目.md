系统:Linux localhost.localdomain 3.10.0-514.el7.x86_64 #1 SMP Tue Nov 22 16:42:41 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux

jdk :21

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







### 3. 配置环境变量

```
# 编辑环境变量文件
sudo vi /etc/profile

# 在文件末尾添加以下内容
export JAVA_HOME=/usr/local/java/jdk-21.0.3  # 根据实际目录名修改
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

### 4. 使环境变量生效

```
# 重新加载环境变量
source /etc/profile

# 验证安装
java -version
javac -version

# 检查 JAVA_HOME
echo $JAVA_HOME
```