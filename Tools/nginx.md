### 查找 Nginx 安装位置

# 查找 Nginx 可执行文件位置
which nginx

# 或者使用 whereis
whereis nginx

# 查找 Nginx 配置文件位置
find / -name nginx.conf 2>/dev/null


## 如果未安装，如何安装 Nginx


### 步骤 1：安装 EPEL 仓库

# 安装 EPEL 仓库
sudo yum install epel-release -y

### 步骤 2：安装 Nginx
# 安装 Nginx
sudo yum install nginx -y

### 步骤 3：启动并设置开机自启

# 启动 Nginx
sudo systemctl start nginx

# 设置开机自启
sudo systemctl enable nginx

# 检查状态
sudo systemctl status nginx

### 步骤 4：配置防火墙（如果需要）


# 开放 80 端口
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload

# 或者直接开放端口
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

### 步骤 5：验证安装

# 查看版本
nginx -v

# 测试访问（如果服务器有公网IP）
curl http://localhost

##  Nginx 重要目录（安装后）

|**<br><br>目录<br><br>**|**<br><br>用途<br><br>**|
|---|---|
|`/etc/nginx/`|配置文件目录|
|`/etc/nginx/nginx.conf`|主配置文件|
|`/etc/nginx/conf.d/`|自定义配置文件目录|
|`/usr/share/nginx/html/`|默认网站目录|
|`/var/log/nginx/`|日志目录|
|`/usr/sbin/nginx`|可执行文件位置|
## 常用 Nginx 命令

# 启动
sudo systemctl start nginx

# 停止
sudo systemctl stop nginx

# 重启
sudo systemctl restart nginx

# 重新加载配置（不中断服务）
sudo systemctl reload nginx

# 查看状态
sudo systemctl status nginx

# 设置开机自启
sudo systemctl enable nginx

# 禁止开机自启
sudo systemctl disable nginx

# 测试配置文件语法
sudo nginx -t



把打包好的前端文件 



1.定位 nginx 配置文件路径 
sudo nginx -t

如果 Nginx 正常运行，它会返回类似这样的信息：
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
其中 /etc/nginx/nginx.conf 就是主配置文件的绝对路径



### 配置 Nginx

在服务器上创建 Nginx 配置文件：

server {
    listen 80;
    server_name your-domain.com;  # 您的域名或IP
    
    root /usr/share/nginx/html;
    index index.html;
    
    # 处理前端路由
    location / {
        try_files $uri $uri/ /index.html;
    }
    
    # 代理 API 请求到后端
    location /prod-api/ {
        proxy_pass http://localhost:8080/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
    
    # 静态文件缓存
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}




### 检查 SELinux (仅限 CentOS/RHEL)
如果你的系统是 CentOS 或 RHEL，SELinux 可能会阻止 Nginx 访问非标准目录（如 `/home` 下的目录）。

临时关闭 SELinux 测试：

sudo setenforce 0
执行后，刷新浏览器。如果 403 错误消失，说明就是 SELinux 的问题。


永久解决方案：不要直接关闭 SELinux，而是为你的项目目录设置正确的 SELinux 上下文。

# 为你的项目目录设置 httpd 系统内容标签 sudo semanage fcontext -a -t httpd_sys_content_t "/home/user/myapp/dist(/.*)?" sudo restorecon -Rv /home/user/myapp/dist


如果 出现
[root@localhost nginx]# sudo setenforce 0
[root@localhost nginx]# sudo semanage fcontext -a -t httpd_sys_content_t "/home/project/ruoyi/dist(/.*)?"
sudo：semanage：找不到命令
[root@localhost nginx]# sudo semanage fcontext -a -t httpd_sys_content_t "/home/project/ruoyi/dist(/.*)?"
sudo：semanage：找不到命令
[root@localhost nginx]# 


第一步：解决 semanage 命令找不到的问题（可选，如果你以后需要管理 SELinux）
如果你想使用 semanage 来管理 SELinux 上下文，你需要先安装它：
sudo yum install policycoreutils-python-utils



tail -f /var/log/nginx/error.log