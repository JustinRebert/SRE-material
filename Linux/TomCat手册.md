yum安装tomcat

下载软件包

```bash
yum install tomcat-webapps tomcat-admin-webapps tomcat-docs-webapp tomcat
```

启动服务

```
systemctl start tomcat.service
systemctl enable tomcat.service
```

二进制安装tomcat

1.关闭Linux防火墙

```bash
systemctl stop firewalld.service #停止firewalld
systemctl disable firewalld.service #禁止firewalld开机启动
firewall-cmd-state #查看防火墙状态
```

2.配置CentOS7本地yum源

```bash
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo #yum基础源
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo #yum epel源
```

3.创建用户

```bash
useradd -d /usr/local/tomcat -s /sbin/nologin tomcat 
```

安装java：详情见 [Java手册](Java手册.md) 

下载tomcat软件包

```bash
wget https://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.5.40/bin/apache-tomcat-8.5.40.tar.gz -O apache-tomcat-8.5.40.tar.gz
```

解压

```bash
tar -xf  apache-tomcat-8.5.40.tar.gz -C /usr/local/
```

创建软连接

```bash
ln -sv /usr/local/apache-tomcat-8.5.40 /usr/local/tomcat
```

更改目录权限

```bash
chown -R tomcat.tomcat /usr/local/tomcat/
```

设置环境变量

```
vim /etc/profile.d/tomcat.sh
export TOMCAT_HOME=/usr/local/tomcat
export PATH=$TOMCAT_HOME/bin:$PATH
source /etc/profile.d/tomcat.sh
```

tomcat部署方式：

```
mkdir -p /usr/local/tomcat/testapp/WEB-INF/{classes,lib}
```

```jsp
vim index.jsp
<%@ page language="java" %>
<%@ page import="java.util.*" %>
<html>
    <head>
        <title>JSP test Page</title>
    </head>
    <body>
        <%
            out.println("Hello world!");
            out.println("Hello Asone!");
        %>
    </body>
</html>
```



tomcat 配置文件

​	server.xml

​	context.xml：常用于定义会话管理器、Readlm和JDBC等

​	INF子目录：常用于定义此会话管理器，Realm以及JDBC等；

​	web.xml：提供基本的sevlet定义和MIME映射表

​	tomcat-users.xml

​	tatalina.policy：当基于-security选项启动tomcat程序时，会读取此配置文件

​	catalina.properties：Java属性定义文件，设定类加载路径、安全包列表和一些调整性能参数信息；

​	logging.properties：定义日志相关的配置信息，如日志级别、文件路径

Tomcat 应用程序“部署”

​	部署是指将webapp及其所依赖类库等装进tomcat实例上，以便接受用户请求：部署方式：

​		静态部署：在tomcat启动之前进行的webapp部署

​		动态部署：在打断tomcat运行的前提下，通过tomcat manager或其他的命令行工具进行的部署

​			TCD：tomcat client deployer

​		部署是一类操作组成：

​			Deploy：将webapp的源文件放置于目标目录、配置tomcat服务能够基于某context路径访问此webapp，并将其特有的类由类加载器进行装载等。

​			Redeploy：重新部署，主要用于升级

​			Undeploy：取消部署，停止应用程序并从tomcat实例上移除其部分文件和部署名；

​			start：将停止的webapp启动起来

​			stop：停止；

​		部署方式：

​			tomcat Manager

​			ANT 脚本

​			TCD：

​			war类归档程序的部署方式：将归档文件复制到￥CATALINA_BASE/webapp/目录中，并长期tomcat即可；tomcat会自动展开war归档；也可以使用tomcat Manager进行热部署。

webapp的体系结构：

​	webapp特有的组织格式，是一种层次型目录结构：通常包含了servlet代码文件、jsp页面文件、类文件、部署描述文件等，一般会打包为归档格式

​	展开的目录：

​		/：web应用程序的根目录

​		/WEB_INF/：此webapp的私有资源目录，通常web.xml和context.xml均放置此目录：

​		/WEB/INF/classes：此web自由的类

​		/WEB_INF/lib：此webapp自有能够给打包为jar格式的类

webapp的归档格式：

​	EJB类归档的扩展名：.jar

​	WEB应用程序的归档扩展名为.war

​	企业级应用程序的扩展名.ear

​	web服务的扩展名为.ear或.war

