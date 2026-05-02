# 📋 Linux CentOS 7 JDK 21 安装部署指南

## 🔍 系统信息确认

```
# 查看系统版本
cat /etc/redhat-release
uname -a
```

## 📦 安装必要工具

### 1. 安装 wget

```
# 安装 wget
yum install -y wget

# 验证安装
wget --version
```

### 2. 检查软件安装状态

```
# 检查是否已安装（以wget为例）
rpm -qa | grep wget

# 检查命令是否存在
which wget

# 快速检查是否安装
rpm -q 软件名

# 查看详细信息
rpm -qi 软件名

# 搜索相关软件
rpm -qa | grep 关键词
```

## ☕ 安装 JDK 21

### 方法一：使用 RPM 包安装（推荐）

```
# 进入下载目录
cd /usr/local/src

# 下载 JDK 21 RPM 包
wget https://download.oracle.com/java/21/latest/jdk-21_linux-x64_bin.rpm

# 安装 RPM 包
rpm -ivh jdk-21_linux-x64_bin.rpm

# 或者使用 yum 安装
yum localinstall jdk-21_linux-x64_bin.rpm

# 验证安装
java -version
javac -version
```

## ⚙️ 配置 JDK 环境变量

### 1. 查找 JDK 安装路径

```
# 查找 Java 安装位置
which java

# 查看真实路径（符号链接）
ls -l /usr/bin/java
ls -l /etc/alternatives/java

# 查看 JDK 目录
ls /usr/lib/jvm/
```

### 2. 配置环境变量

```
# 编辑环境变量文件
vi /etc/profile

# 按 i 进入编辑模式
# 在文件末尾添加以下内容：
```

```
# JDK 环境配置
export JAVA_HOME=/usr/lib/jvm/jdk-21  # 根据实际路径修改
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
export PATH=$JAVA_HOME/bin:$PATH
```

vi 编辑器操作：

- 保存并退出：按 `ESC`，然后输入 `:wq` + `回车`
- 不保存退出：按 `ESC`，然后输入 `:q!` + `回车`

### 3. 使配置生效

```
# 重新加载环境变量
source /etc/profile

# 验证配置
echo $JAVA_HOME
echo $PATH
java -version
javac -version
```

### 4. 测试编译运行

```
# 创建测试文件
echo 'public class Test { 
    public static void main(String[] args) { 
        System.out.println("JDK配置成功!"); 
    } 
}' > Test.java

# 编译运行
javac Test.java
java Test
```

## 🔧 高级配置

### 使用 alternatives 配置默认 Java

```
# 查看所有 Java 版本
alternatives --config java

# 设置默认 Java 版本
alternatives --set java /usr/lib/jvm/jdk-21/bin/java
alternatives --set javac /usr/lib/jvm/jdk-21/bin/javac
```

### 快速配置脚本

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
```

## 🚀 部署 Java 项目

### 1. 准备项目文件

```
# 创建项目目录
mkdir -p /opt/myjavaapp
cd /opt/myjavaapp

# 上传 jar/war 文件（使用 scp 或 rz 命令）
# scp your-application.jar user@server:/opt/myjavaapp/

# 移动文件
mv /path/to/your-application.jar /opt/myjavaapp/
```

### 2. 运行 Java 项目

```
# 进入项目目录
cd /opt/myjavaapp

# 给执行权限
chmod +x your-application.jar

# 运行 jar 包
java -jar your-application.jar

# 指定配置文件
java -jar your-application.jar --spring.config.location=application.properties

# 后台运行
nohup java -jar your-application.jar > app.log 2>&1 &
```

### 3. 监控项目运行

```
# 查看 Java 进程
ps -ef | grep java

# 查看端口占用
netstat -tlnp | grep java

# 查看日志
tail -f app.log
```

## 🛠️ 常见问题解决

```
# 权限问题
chmod +x your-application.jar

# 端口被占用
java -jar your-application.jar --server.port=8081

# 内存设置
java -Xms512m -Xmx1024m -jar your-application.jar

# 检查环境变量
env | grep JAVA
```

## 📊 验证清单

- [ ]  wget 已安装
- [ ]  JDK 21 已安装
- [ ]  环境变量已配置
- [ ]  Java 版本验证通过
- [ ]  项目目录已创建
- [ ]  项目文件已上传
- [ ]  项目正常运行

---

💡 提示： 每次修改 `/etc/profile` 后都需要执行 `source /etc/profile` 使配置生效，或者重新登录系统。

这样整理后的指南更加清晰易读，方便您按步骤操作！