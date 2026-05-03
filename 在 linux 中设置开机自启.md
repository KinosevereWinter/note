第一步：准备部署目录
建议将你的 `ruoyi-admin.jar` 移动到一个固定的目录，方便管理（例如 `/opt/ruoyi`）：


```
# 创建部署目录 mkdir -p /opt/ruoyi # 将你打包好的 jar 包复制过去（请根据你 jar 包的实际路径修改） cp /你的jar包路径/ruoyi-admin.jar /opt/ruoyi/
```


二步：创建 Systemd 服务配置文件


使用 `vim` 创建一个新的服务文件：
vim /etc/systemd/system/ruoyi.service


按 i 进入编辑模式，将以下内容复制进去（注意修改 ExecStart 中 Java 的实际路径）：


[Unit] Description=RuoYi Admin Service 
After=network.target 
[Service] 
Type=simple 
User=root 
WorkingDirectory=/opt/ruoyi 
# 下面这行请确保 java 的路径正确，如果不确定 java 在哪，可以执行 `which java` 查看 
ExecStart=/usr/bin/java -jar /opt/ruoyi/ruoyi-admin.jar 

Restart=on-failure 
RestartSec=10 
[Install] 
WantedBy=multi-user.target
编辑完成后，按 Esc 键，输入 :wq 保存并退出。

### 第三步：启动并设置开机自启

配置好服务文件后，依次执行以下命令：

# 1. 重新加载 systemd 配置，让新创建的服务生效
systemctl daemon-reload

# 2. 启动若依服务

systemctl start ruoyi

# 3. 设置开机自启（实现你想要的开关自启） systemctl enable ruoyi

第四步：验证与查看日志

1**查看服务状态**：
systemctl status ruoyi

如果看到绿色的 active (running)，说明后台运行成功。

查看实时日志（替代之前的控制台输出）：
journalctl -u ruoyi -f


常用管理命令
以后你就可以通过以下命令来管理若依项目了，完全不需要再手动敲 java -jar：
停止服务：systemctl stop ruoyi
重启服务：systemctl restart ruoyi
查看状态：systemctl status ruoyi


