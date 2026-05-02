第一步：安装 Redis
由于你的系统可能默认源中的 Redis 版本较旧，为了获得更好的兼容性，建议先安装 EPEL 仓库（Extra Packages for Enterprise Linux），或者使用 Remi 仓库。
执行以下命令：
```
1. 安装 EPEL 仓库（包含 Redis 包） 
   
   yum install epel-release -y 
   
 2. 安装 Redis yum install redis -y
   
```


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


给 Redis 设置密码是保障数据安全的重要一步。对于你的若依（RuoYi）系统，我们需要分两步走：第一步是修改 Redis 服务端的配置文件，开启并设置密码；第二步是修改若依项目的配置文件，让项目知道新密码


第一步：修改 Redis 配置文件（服务端)

打开配置文件
通常 Redis 的配置文件位于 /etc/redis.conf。使用 vim 或你喜欢的编辑器打开它：

vi /etc/redis.conf


搜索 requirepass
在编辑器中按 / 键，输入 requirepass，然后按回车查找。
你会看到一行被注释掉的代码：# requirepass foobared


置你的密码
去掉行首的 # 号（取消注释）。
将 foobared 修改为你想要设置的密码。
示例（假设密码设为 ruoyi123）：

requirepass ruoyi123


保存并退出
按 Esc 键，输入 :wq 保存并退出。

为了让配置生效，必须重启 Redis。在终端执行：

systemctl restart redis