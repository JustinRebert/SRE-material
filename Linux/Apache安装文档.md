[TOC]



# 一、引言

## 1.1 简介

Apache是世界使用排名第一的Web服务器软件。它可以运行在几乎所有广泛使用的计算机平台上，由于其跨平台和安全性被广泛使用，是最流行的Web服务器端软件之一。

1995 年 4 月, 最早的 apache(0.6.2 版)由 apache group 公布发行。apache group 是一个完全通过 internet 进行运作的非盈利机构, 由它来决定apache web 服务器的标准发行版中应该包含哪些内容。准许任何人修改隐错,提供新的特征和将它移植到新的平台上, 以及其它的工作。当新的代码被提交给apache group 时, 该团体审核它的具体内容, 进行测试 如果认为满意, 该代码就会被集成到 apache 的主要发行版中。

Apache 的发展时期很长，而且是毫无争议的世界第一大服务器。它有着很多有点：稳定、开源、跨平台等等。但是由于它出现的时间太长了。它兴起的年代，互联网产业远比不上现在，所以它被设计为一个重量级的，不支持高并发的服务器。在 Apache 上运行数以万计的并发访问，会导致服务器消耗大量内存。操作系统对其进行进程或线程间的切换也消耗了大量的 CPU 资源，导致HTTP 请求的平均响应速度降低。

## 1.2 特性

- 几乎可以运行在所有的计算机平台上
- 支持最新的 http/1.1 协议
- 简单而且强有力的基于文件的配置(httpd.conf)
- 支持通用网关接口(cgi)
- 支持虚拟主机
- 支持 http 认证
- 集成 perl
- 集成的代理服务器
- 可以通过 web 浏览器监视服务器的状态, 可以自定义日志
- 支持服务器端包含命令(ssi)
- 支持安全 socket 层(ssl)
- 具有用户会话过程的跟踪能力
- 支持 fastcgi
- 支持 Java

# 二、架构

## 2.1 Apache架构

表2-1Apache源码介绍

| 文件夹  | 功能说明                                                     |
| :------ | :----------------------------------------------------------- |
| server  | 是Apache的核心功能，包括了请求处理、协议处理等，同时Apache的main.c文件中的main()是Apache启动时的入口（Apache的启动过程我打算专门写一篇经验来说明） |
| modules | 保存着Apache的所有模块，例如认证模块aaa，代理模块Proxy等等   |
| include | 包含了Apache中必须的头文件，由一堆.h文件组成，例如http_config.h,http_core.h,http_request.h等，Apache的请求处理函数的声明、全局变量等等都在这里 |
| srclib  | 包含了Apache开发和运行所需要的基础库，包括apr库，pcre库等等  |
| os      | 包含了各个操作系统所特有的内容                               |
| docs    | 包含了Apache的文档                                           |
| test    | 包含了很多对APR库使用的测试，对于大部分APR的API使用都给出了示例，可以作为开发时候的参考 |

## 2.2. Apache层次结构

Apache的设计层次结构如下图所示，主要分为五层，可以类比计算机网络体系结构

![å¾ç1.png](http://assets.tianmaying.com/md-image/524a73a74916254098843d5f37a76db5.png)



### 2.2.1操作系统层

Apache归根结底是建立在操作系统的普通的应用程序上的，因此必须使用操作系统本身提供的底层功能，比如进程和线程、进程和线程间的通信、网络套接字通信、文件系统等等。目前Apache可以支持五大操作系统：Unix\OS/2\Windows\MacOS\NetWare

### 2.2.2可移植运行库层

早期的Apache只用于Unix系统，后来为了能够跨平台使用，不同的操作系统提供的底层API不同，甚至存在很大差异，需要隐藏不同操作系统的API细节问题，对所有的操作系统，提供一个完全相同的函数接口从Apache2.0开始将专门方庄不同操作系统API的任务独立出来形成新的项目，称为APR(Apache Portable Runtime libraries)，主要为上层的应用程序提供一个可以跨越多操作系统平台使用的底层支持接口库。

### 2.2.3核心功能层

这是Apache的核心部分，它用来实现Apache的基本功能以及对其他模块的支持调用等，包括了两个部分：Apache核心程序和Apache核心模块。

#### 2.2.3.1Apache核心程序

Apache核心程序主要实现Apache作为HTTP服务器的基本功能，包括：

- 启动、停止和重启Apache
- 处理配置文件例如httpd.conf等所有的配置文件
- 接受和处理HTTP连接
- 读取HTTP请求并对请求进行处理
- 处理HTTP协议等

#### 2.2.3.2Apache核心模块

Apache只是一个HTTP服务器，本身只有很简单的上述基本功能，为了完成其他功能则需要开发相应的模块。为了最大程度的将核心功能与模块解耦，Apache采用了对模块进行载入的方式，如果需要启用某些模块，则只需要编辑相应的配置文件将该模块载入，Apache在启动时读取配置文件进行处理（读取到配置指令LoadModule然后执行mod_so模块中的该指令对应的指令处理函数，将模块载入进来即可。Apache的核心模块则提供了这些扩展功能

| Apache核心模块 | Apache核心模块功能                                           |
| :------------- | :----------------------------------------------------------- |
| mod_core       | 处理配置文件中的大部分配置指令，并根据指令运行Apache         |
| mod_so         | 负责动态加载其余的模块，缺少该模块其余的模块就无法被加载，因此该模块必须静态加载（在Apache启动初期加载进来，Apache如何加载 |
| MPM模块        | 负责Apache中的并发模型，例如prefork,worker等，在Apache初始化完成之后进入ap_mpm_run函数，然后生成多个进程，每个进程又产生多个线程，对端口进行监听，每个线程对一个请求进行处理 |

### 2.2.4. 可选功能层

Apache本身只具备上述简单的功能，为了完成其它功能需要开发响应的模块，可选功能层指所有的非核心模块的其他Apache模块，实际上对于服务器端开发我们通常指的就是这个层，开发者开发自己相应的模块，对于模块开发会写另一篇经验

### 2.2.5. 可选功能层

Apache的模块开发中使用到了第三方的开发库，例如mod_ssl使用了OpenSSL，mod_perl使用了Perl开发库等，这个部分都放在了srclib中

## 2.3. Apache核心功能层

这是Apache的基础和核心部分，正如前面所受，它实现了Apache作为HTTP服务器的基本功能以及对模块的扩展，我对这个部分用一张图来进行介绍

![å¾ç2.png](http://assets.tianmaying.com/md-image/e630971d05b8bd6dcc90e9b9ee7b5100.png)



Apache核心模块与Apache可选模块的接口完全相同，对于Apache核心而言完全相同；核心模块和非核心模块的唯一区别在于加载的时间不同，核心模块通常必须静态加载，非核心模块既可以静态加载，也可以动态加载而Apache核心由几个核心组件实现，核心组件如下所示

| 核心组件名称  | 对应文件                                                     | 组件功能                                                     |
| :------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| HTTP_CONFIG   | http_config.c                                                | 对配置文件进行解析、处理和保存； 提供对配置数据访问的接口，其余组件在配置数据的任何时候都能够快地返回配置信息； |
| HTTP_CONNECT  | http_connection.h connection.c                               | 负责HTTP连接                                                 |
| HTTP_PROTOCOL | http_protocol.h http_protocol.c                              | 负责处理HTTP/1.0和HTTP/1.1协议的解析，例如解析http请求头、生成返回给客户的响应包等等 |
| HTTP_REQUEST  | http_request.h http_request.c request.c                      | 负责对请求进行处理                                           |
| MPM           | MPM文件夹，Apache2.x已经实现了多种MPM模型，但推荐使用worker模型 | MPM负责为Apache系统提供可靠、稳定、高效的进程和线程的并发处理 |

# 三、安装与部署

环境：CentOS 7 x86_64

## 3.1 yum安装

（1）安装软件包

```
yum install httpd -y
```

（2）关闭selinux和防火墙

```
setenforce 0
systemctl stop firewalld.service
```

（3）主要目录或文件

```
/etc/httpd/conf/httpd.conf #主配置文件
/etc/httpd/conf/conf.d/*.conf #其他配置文件
/etc/httpd/conf.modules.d/ #模块文件配置目录
/usr/sbin/httpd #主程序
/usr/share/man/man8/httpd.8.gz #帮助文档
/usr/lib/systemd/system/httpd.service #服务启动文件
/var/www/html #对外提供访问默认目录
```

（4）主配置文件示例

```
> grep -v "#" /etc/httpd/conf/httpd.conf | grep -v "^$"
ServerRoot "/etc/httpd"
Listen 80
Include conf.modules.d/*.conf
User apache
Group apache
ServerAdmin root@localhost
<Directory />
    AllowOverride none
    Require all denied
</Directory>
DocumentRoot "/var/www/html"
<Directory "/var/www">
    AllowOverride None
    Require all granted
</Directory>
<Directory "/var/www/html">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>
<Files ".ht*">
    Require all denied
</Files>
ErrorLog "logs/error_log"
LogLevel warn
<IfModule log_config_module>
    LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
    LogFormat "%h %l %u %t \"%r\" %>s %b" common
    <IfModule logio_module>
      LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\" %I %O" combinedio
    </IfModule>
    CustomLog "logs/access_log" combined
</IfModule>
<IfModule alias_module>
    ScriptAlias /cgi-bin/ "/var/www/cgi-bin/"
</IfModule>
<Directory "/var/www/cgi-bin">
    AllowOverride None
    Options None
    Require all granted
</Directory>
<IfModule mime_module>
    TypesConfig /etc/mime.types
    AddType application/x-compress .Z
    AddType application/x-gzip .gz .tgz
    AddType text/html .shtml
    AddOutputFilter INCLUDES .shtml
</IfModule>
AddDefaultCharset UTF-8
<IfModule mime_magic_module>
    MIMEMagicFile conf/magic
</IfModule>
EnableSendfile on
IncludeOptional conf.d/*.conf
```

(5)查看/usr/lib/systemd/system/httpd.service文件内容

```
grep -v "#" /usr/lib/systemd/system/httpd.service | grep -v "^$"
[Unit]
Description=The Apache HTTP Server
After=network.target remote-fs.target nss-lookup.target
Documentation=man:httpd(8)
Documentation=man:apachectl(8)
[Service]
Type=notify
EnvironmentFile=/etc/sysconfig/httpd
ExecStart=/usr/sbin/httpd $OPTIONS -DFOREGROUND
ExecReload=/usr/sbin/httpd $OPTIONS -k graceful
ExecStop=/bin/kill -WINCH ${MAINPID}
KillSignal=SIGCONT
PrivateTmp=true
[Install]
WantedBy=multi-user.target
```

（6）检查配置文件并启动服务

```
httpd -t
systemctl start httpd.service

```

（7）测试

![1553561442249](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553561442249.png)

## 3.2 源码编译安装：

（1）安装依赖包

```
yum install -y wget make gcc gcc-c++ pcre openssl openssl-devel zlib unzip cmake ncurses-devel libjpeg libjpeg-devel libpng libpng-devel libxml2 libxml2-devel curl-devel libtool libtool-ltdl libtool-ltdl-devel libevent libevent-devel zlib-static zlib-devel autoconf pcre-devel gd perl freetype freetype-devel expat-devel
```

（2）获取源代码

```
cd /usr/local/src/
wget http://mirrors.shu.edu.cn/apache//httpd/httpd-2.4.38.tar.bz2
wget http://mirrors.tuna.tsinghua.edu.cn/apache//apr/apr-1.6.5.tar.bz2
wget http://mirrors.tuna.tsinghua.edu.cn/apache//apr/apr-util-1.6.1.tar.gz
tar xvf httpd-2.4.38.tar.bz2
tar xvf apr-1.6.5.tar.bz2
tar xvf apr-util-1.6.1.tar.gz
cp apr-1.6.5 httpd-2.4.38/srclib/apr -r
cp apr-util-1.6.1 httpd-2.4.38/srclib/apr-util -r
```

（3）安装配置

```
cd /usr/local/src/httpd-2.4.38
> ./configure \
--prefix=/usr/local/httpd \
--enable-so \
--enable-ssl \
--enable-cgi \
--enable-rewrite \
--with-zlib \
--with-pcre \
--with-included-apr \
--enable-modules=most \
--enable-mpms-shared=all \
--with-mpm=prefork
```

（4）编译与安装

```
make
make install
```

（5）查看生成的文件

```
cd /usr/local/httpd/
tree -L 1
.
├── bin
├── build
├── cgi-bin
├── conf
├── error
├── htdocs
├── icons
├── include
├── lib
├── logs
├── man
├── manual
└── modules
```

（6）默认的配置文件示例

```
grep -v "#" /usr/local/httpd/conf/httpd.conf | grep -v "^$"
ServerRoot "/usr/local/httpd"
Listen 80
LoadModule mpm_prefork_module modules/mod_mpm_prefork.so
LoadModule authn_file_module modules/mod_authn_file.so
LoadModule authn_core_module modules/mod_authn_core.so
LoadModule authz_host_module modules/mod_authz_host.so
LoadModule authz_groupfile_module modules/mod_authz_groupfile.so
LoadModule authz_user_module modules/mod_authz_user.so
LoadModule authz_core_module modules/mod_authz_core.so
LoadModule access_compat_module modules/mod_access_compat.so
LoadModule auth_basic_module modules/mod_auth_basic.so
LoadModule reqtimeout_module modules/mod_reqtimeout.so
LoadModule filter_module modules/mod_filter.so
LoadModule mime_module modules/mod_mime.so
LoadModule log_config_module modules/mod_log_config.so
LoadModule env_module modules/mod_env.so
LoadModule headers_module modules/mod_headers.so
LoadModule setenvif_module modules/mod_setenvif.so
LoadModule version_module modules/mod_version.so
LoadModule unixd_module modules/mod_unixd.so
LoadModule status_module modules/mod_status.so
LoadModule autoindex_module modules/mod_autoindex.so
<IfModule !mpm_prefork_module>
</IfModule>
<IfModule mpm_prefork_module>
</IfModule>
LoadModule dir_module modules/mod_dir.so
LoadModule alias_module modules/mod_alias.so
<IfModule unixd_module>
User daemon
Group daemon
</IfModule>
ServerAdmin you@example.com
<Directory />
    AllowOverride none
    Require all denied
</Directory>
DocumentRoot "/usr/local/httpd/htdocs"
<Directory "/usr/local/httpd/htdocs">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>
<Files ".ht*">
    Require all denied
</Files>
ErrorLog "logs/error_log"
LogLevel warn
<IfModule log_config_module>
    LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
    LogFormat "%h %l %u %t \"%r\" %>s %b" common
    <IfModule logio_module>
      LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\" %I %O" combinedio
    </IfModule>
    CustomLog "logs/access_log" common
</IfModule>
<IfModule alias_module>
    ScriptAlias /cgi-bin/ "/usr/local/httpd/cgi-bin/"
</IfModule>
<IfModule cgid_module>
</IfModule>
<Directory "/usr/local/httpd/cgi-bin">
    AllowOverride None
    Options None
    Require all granted
</Directory>
<IfModule headers_module>
    RequestHeader unset Proxy early
</IfModule>
<IfModule mime_module>
    TypesConfig conf/mime.types
    AddType application/x-compress .Z
    AddType application/x-gzip .gz .tgz
</IfModule>
<IfModule proxy_html_module>
Include conf/extra/proxy-html.conf
</IfModule>
<IfModule ssl_module>
SSLRandomSeed startup builtin
SSLRandomSeed connect builtin
</IfModule>
```

（7）启动脚本

```
grep -v "#" /usr/local/httpd/bin/apachectl | grep -v "^$"
ACMD="$1"
ARGV="$@"
HTTPD='/usr/local/httpd/bin/httpd'
if test -f /usr/local/httpd/bin/envvars; then
  . /usr/local/httpd/bin/envvars
fi
LYNX="lynx -dump"
STATUSURL="http://localhost:80/server-status"
ULIMIT_MAX_FILES="ulimit -S -n `ulimit -H -n`"
if [ "x$ULIMIT_MAX_FILES" != "x" ] ; then
    $ULIMIT_MAX_FILES
fi
ERROR=0
if [ "x$ARGV" = "x" ] ; then 
    ARGV="-h"
fi
case $ACMD in
start|stop|restart|graceful|graceful-stop)
    $HTTPD -k $ARGV
    ERROR=$?
    ;;
startssl|sslstart|start-SSL)
    echo The startssl option is no longer supported.
    echo Please edit httpd.conf to include the SSL configuration settings
    echo and then use "apachectl start".
    ERROR=2
    ;;
configtest)
    $HTTPD -t
    ERROR=$?
    ;;
status)
    $LYNX $STATUSURL | awk ' /process$/ { print; exit } { print } '
    ;;
fullstatus)
    $LYNX $STATUSURL
    ;;
*)
    $HTTPD "$@"
    ERROR=$?
esac
exit $ERROR
```

（8）检查配置文件并启动服务

```
/usr/local/httpd/bin/httpd -t
/usr/local/httpd/bin/apachectl stop
```

# 四、常用配置

4.1支持https：

