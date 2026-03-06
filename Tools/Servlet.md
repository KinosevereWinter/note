## servlet原理
```
1.浏览器首先发送请求:http://localhost:8080/day01_servlet/quick
2.DNS域名解析
3.Tomcat解析请求
	上下文路径(应用的名称):day01_servlet
	资源在名称：quick
4.从web的根路径/WEB-INF下找到web.xml文件
5.读取web.xml文件获取所有的<url-patten>元素，并判断那一个<url-patten>的内容是/quick
	如果能找到：执行下步操作
	如果找不到：报404资源找不到
6.通过/quick,找到当前Servlet的全限定名。
	com.yunhe.QuickServlet
7.从Servlet的实例缓冲池中去取com.yunhe.QuickServlet对应的对象。
8.使用反射创建Servlet对象
9.把创建的Servlet对象，存储到Servlet实例缓存池中，提供下一次请求使用。
	Map<String,Servlet>
10.容器创建ServletConfig对象，并调用init方法来完成初始化操作
11.容器创建ServletRequest和ServletResponse对象，并调用service方法，处理请求
12.在service方法中，对当前请求的客户端做响应
```

![[Pasted image 20260306235956.png]]![image-20260307000034177](https://cdn.jsdelivr.net/gh/Q1026076464/picx-images-hosting/article/20260307000034276.png)