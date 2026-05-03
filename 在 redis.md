





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