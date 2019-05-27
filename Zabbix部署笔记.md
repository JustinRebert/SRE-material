# 安装nginx：

```bash
yum install -y vim lrzsz tree screen psmisc lsof tcpdump wget ntpdate gcc gcc-c++ glibc glibc-devel pcre pcre-devel openssl openssl-devel systemd-devel net-tools iotop bc zip unzip zlib-devel bash-completion nfs-utils automake libxml2 libxml2-devel libxslt libxslt-devel perl perl-ExtUtils-Embed

tar xf /usr/local/src/nginx-1.14.2.tar.gz -C /usr/local/src/

./configure --prefix=/usr/local/nginx --user=www --group=www --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-http_stub_status_module --with-http_gzip_static_module --with-pcre --with-stream --with-stream_ssl_module --with-stream_realip_module --with-http_stub_status_module --with-pcre

make && make install

vim /etc/profile.d/nginx.sh
export PATH=/usr/local/nginx/sbin:$PATH

source /etc/profile.d/nginx.sh
useradd www -s /sbin/nologin

chown www.www -R /usr/local/nginx/

vim /usr/local/nginx/conf/nginx.conf
pid /usr/local/logs/nginx.pid

vim /usr/lib/systemd/system/nginx.service
[Unit]
Description=The nginx HTTP and reverse proxy server
After=network.target remote-fs.target nss-lookup.target
[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/bin/rm -f /usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/bin/kill -s HUP $MAINPID
KillSignal=SIGQUIT
TimeoutStopSec=5
KillMode=process
PrivateTmp=true
[Install]
WantedBy=multi-user.target

systemctl damon-reload
```

# 配置nginx

```bash
vim /usr/local/nginx/conf/nginx.conf
include /usr/local/nginx/conf.d/*.conf;

mkdir /usr/local/nginx/conf.d

vim /usr/local/nginx/conf.d/zabbix.conf
server {
	listen 80;
	location / {
		root /data/nginx/html;
		index index.htm index.html index.php;
	}
	location ~\.php$ {
		root /data/nginx/html;
		fastcgi_pass 127.0.0.1:9000;
		fastcgi_index index.php;
		fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
		include fastcgi_params;
	}
}

systemctl restart nginx.service
```

# 安装php：

```bash
yum -y install pcre pcre-devel openssl openssl-devel libicu-devel gcc gcc-c++ autoconf libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel glibc glibc-devel glib2 glib2-devel ncurses ncurses-devel curl curl-devel krb5-devel libidn libidn-devel openldap openldap-devel openldap-servers openldap-clients nss_ldap jemalloc-devel cmake boost-devel bison automake libevent libevent-devel gd gd-devel libtool libmcrypt libmcrypt-devel mcrypt mhash libxslt libxslt-devel readline readline-devel gmp gmp-devel libcurl libcurl-devel openjpeg-devel bzip2 bzip2-devel libxml2 libxml2-devel bzip2 bzip2-devel curl-devel  gmp-devel readline-devel libxslt-devel

tar xf /usr/local/src/php-7.1.2.tar.gz -C /usr/local/src/

./configure --prefix=/usr/local/php --with-pdo-mysql=/opt/mysql --enable-mysqlnd --with-pdo-mysql=mysqlnd -with-mysqli=mysqlnd --with-mysql-sock=/tmp/mysql.sock --with-config-file-path=/usr/local/php --enable-fpm --enable-inline-optimization --disable-debug --disable-rpath --enable-shared --enable-soap --with-libxml-dir --with-xmlrpc --with-openssl --with-mhash --with-pcre-regex --with-sqlite3 --with-zlib --enable-bcmath --with-iconv --with-bz2 --enable-calendar --with-curl --with-cdb --enable-dom --enable-exif --enable-fileinfo --enable-filter --with-pcre-dir --enable-ftp --with-gd --with-openssl-dir --with-jpeg-dir --with-png-dir --with-zlib-dir --with-freetype-dir --enable-gd-jis-conv --with-gettext --with-gmp --with-mhash --enable-json --enable-mbstring --enable-mbregex --enable-mbregex-backtrack --with-onig --enable-pdo --with-zlib-dir --with-pdo-sqlite --with-readline --enable-session --enable-shmop --enable-simplexml --enable-sockets --enable-sysvmsg --enable-sysvsem --enable-sysvshm --enable-wddx --with-libxml-dir --with-xsl --enable-zip --with-pear --enable-opcache

make && make install

useradd www -s /sbin/nologin

cp /usr/local/php/etc/php-fpm.d/www.conf.default /usr/local/php/etc/php-fpm.d/www.conf

cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf

cp /usr/local/src/php-7.1.2/php.ini-production /usr/local/php/etc/php.ini

mkdir /usr/local/php/run

vim /usr/local/php/etc/php-fpm.d/www.conf
pm.max_children = 50
pm.start_servers = 30
pm.min_spare_servers = 30
pm.max_spare_servers = 35

mkdir /usr/local/php/log

vim /etc/profile.d/php.sh
export PATH=/usr/local/php/sbin:$PATH

source /etc/profile.d/php.sh

vim /usr/lib/systemd/system/php-fpm.service
[Unit]
Description=The PHP FastCGI Process Manager
After=syslog.target network.target

[Service]
Type=simple
PIDFile=/usr/local/php/run/php-fpm.pid
ExecStart=/usr/local/php/sbin/php-fpm -c /usr/local/php/etc/php.ini --nodaemonize --fpm-config /usr/local/php/etc/php-fpm.conf
ExecReload=/bin/kill -USR2 $MAINPID
ExecStop=/bin/kill -SIGINT $MAINPID

[Install]
WantedBy=multi-user.target

systemctl damon-reload

systemctl start php-fpm.service
```

# 配置php

```bash
vim /usr/local/php/etc/php.ini
memory_limit = 128M
post_max_size = 16M
upload_max_filesize = 2M
max_execution_time = 300
max_input_time = 300
session.auto_start = 0
mbstring.func_overload = 0
always_populate_raw_post_data = -1
```

# 安装Mariadb通用版

```bash
yum install vim gcc gcc-c++ wget autoconf libaio libaio-devel net-tools lrzsz iotop lsof iotop bash-completion curl policycoreutils openssh-server openssh-clients postfix -y

tar xf /usr/local/src/mariadb-10.2.23-linux-x86_64.tar.gz -C /usr/local/src/

ln -s /usr/local/src/mariadb-10.2.23-linux-x86_64 /usr/local/mysql

useradd -s /sbin/nologin mysql

mkdir -p /data/mysql/data/

/usr/local/mysql/scripts/mysql_install_db --basedir=/usr/local/mysql --datadir=/data/mysql/data --user=mysql

cp /usr/local/mysql/support-files/my-huge.cnf /etc/my.cnf

vim /etc/my.cnf
[client]
port		= 3306
socket		= /data/mysql/data/mysql.sock

[mysqld]
port		= 3306
datadir=/data/mysql/data
basedir=/usr/local/mysql
socket		= /data/mysql/data/mysql.sock
skip-external-locking
key_buffer_size = 384M
max_allowed_packet = 1M
table_open_cache = 512
sort_buffer_size = 2M
read_buffer_size = 2M
read_rnd_buffer_size = 8M
myisam_sort_buffer_size = 64M
thread_cache_size = 8
query_cache_size = 32M
innodb_file_per_table = 1
max_connections = 10000
thread_concurrency = 8
log-bin=mysql-bin
server-id	= 1

[mysqldump]
quick
max_allowed_packet = 16M

[mysql]
no-auto-rehash
[myisamchk]
key_buffer_size = 256M
sort_buffer_size = 256M
read_buffer = 2M
write_buffer = 2M
[mysqlhotcopy]
interactive-timeout

cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mariadb

chmod +x /etc/init.d/mariadb

vim /etc/profile.d/mysql.sh
export MYSQL_HOME=/usr/local/mysql
export PATH=$MYSQL_HOME/bin:$MYSQL_HOME/scripts:$PATH

source /etc/profile.d/mysql.sh

vim /usr/lib/systemd/system/mariadb.service
[Unit]
Description=MariaDB database server
After=syslog.target
After=network.target

[Service]
Type=simple
User=mysql
Group=mysql
ExecStart=/usr/local/mysql/bin/mysqld_safe --basedir=/usr/local/mysql/
TimeoutSec=300
PrivateTmp=true

[Install]
WantedBy=multi-user.target

systemctl damon-reload
systemctl start mariadb.service
```

# 配置数据库

```mysql
mysql

create database zabbix character set utf8 collate utf8_bin;

grant all privileges on zabbix.* to zabbix@"172.16.100.%" identified by '123456';

flush privileges;
```

# 安装java

```bash
yum install -y glibc-devel

tar xf /usr/local/src/jdk-8u211-linux-i586.tar.gz -C /usr/local/src/

ln -s /usr/local/src/jdk1.8.0_211 /usr/local/java

[root@localhost ~]# vim /etc/profile.d/java.sh 
export JAVA_HOME=/usr/local/java
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HONE/lib/dt.jar$JAVA_HOME/lib/tools.jar

source /etc/profile.d/java.sh
```

# 安装zabbix-server

```bash
yum install libevent libevent-devel libaio gcc libxml* unixODBC-devel mysql-devel net-snmp-devel libxml2-devel libcurl-devel libevent-devel pcre* -y

tar xf /usr/local/src/zabbix-4.0.6.tar.gz -C /usr/local/src/

useradd zabbix -s /sbin/nologin

./configure --prefix=/usr/local/zabbix --enable-server --enable-proxy --enable-agent --with-mysql --with-net-snmp --with-libcurl --with-libxml2 --enable-java

make
make install

mysql -uzabbix -p123456 -h172.16.100.1 zabbix < /usr/local/src/zabbix-4.0.6/database/mysql/schema.sql 

mysql -uzabbix -p123456 -h172.16.100.1 zabbix < /usr/local/src/zabbix-4.0.6/database/mysql/images.sql 

mysql -uzabbix -p123456 -h172.16.100.1 zabbix < /usr/local/src/zabbix-4.0.6/database/mysql/data.sql

cp /usr/local/src/zabbix-4.0.6/misc/init.d/fedora/core/zabbix_* /etc/init.d/

vim /etc/init.d/zabbix_server
vim /etc/init.d/zabbix_agent
22 BASEDIR=/usr/local/zabbix

grep "^[a-Z]" /usr/local/zabbix/etc/zabbix_server.conf
LogFile=/var/log/zabbix/zabbix_server.log
DBHost=172.16.100.1
DBName=zabbix
DBUser=zabbix
DBPassword=123456
DBPort=3306
Timeout=30
LogSlowQueries=3000

cp /var/www/html/zabbix/conf/zabbix.conf.php.example /var/www/html/zabbix/conf/zabbix.conf.php

```

出错：

![1555737575748](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1555737575748.png)

# 第一步

在您的浏览器打开 Zabbix 链接：http://<server_ip_or_name>/zabbix

您可以看到前端安装向导的第一个页面。

![img](https://www.zabbix.com/documentation/4.0/_media/manual/installation/install_1_a.png?cache=)

# 第二步

请确认满足所有的软件安装前置条件。

![img](https://www.zabbix.com/documentation/4.0/_media/manual/installation/install_2.png?cache=)

| 先决条件                                   | 最低要求                                       | 描述                                                         |
| :----------------------------------------- | :--------------------------------------------- | :----------------------------------------------------------- |
| *PHP 版本*                                 | 5.4.0                                          |                                                              |
| *PHP memory_limit 选项*                    | 128MB                                          | 位于 php.ini: memory_limit = 128M                            |
| *PHP post_max_size 选项*                   | 16MB                                           | 位于 php.ini: post_max_size = 16M                            |
| *PHP upload_max_filesize 选项*             | 2MB                                            | 位于 php.ini: upload_max_filesize = 2M                       |
| *PHP max_execution_time 选项*              | 300 seconds (此值允许为 0 和 -1 )              | 位于 php.ini: max_execution_time = 300                       |
| *PHP max_input_time 选项*                  | 300 seconds (此值允许为 0 和 -1)               | 位于 php.ini: max_input_time = 300                           |
| *PHP session.auto_start 选项*              | 必须禁用此值                                   | In php.ini: session.auto_start = 0                           |
| *数据库支持*                               | 其中之一： MySQL,、Oracle、PostgreSQL、IBM DB2 | 必须安装下列模块中的一种： mysql、oci8、pgsql、ibm_db2       |
| *bcmath*                                   |                                                | php-bcmath                                                   |
| *mbstring*                                 |                                                | php-mbstring                                                 |
| *PHP mbstring.func_overload 选项*          | 必须禁用此值                                   | 位于 php.ini: mbstring.func_overload = 0                     |
| *PHP always_populate_raw_post_data option* | 必须禁用此值                                   | 只适用于 PHP 5.6.0 或更高的版本。 位于 php.ini: always_populate_raw_post_data = -1 |
| *sockets*                                  |                                                | php-net-socket 用于支持用户脚本。                            |
| *gd*                                       | 2.0 或更高                                     | php-gd. PHP GD 扩展必须支持 PNG 图像 (*--with-png-dir*),、JPEG (*--with-jpeg-dir*) 图像和 FreeType 2 (*--with-freetype-dir*). |
| *libxml*                                   | 2.6.15                                         | php-xml or php5-dom                                          |
| *xmlwriter*                                |                                                | php-xmlwriter                                                |
| *xmlreader*                                |                                                | php-xmlreader                                                |
| *ctype*                                    |                                                | php-ctype                                                    |
| *session*                                  |                                                | php-session                                                  |
| *gettext*                                  |                                                | php-gettext 从 Zabbix 2.2.1 起，PHP gettext 扩展不是安装 Zabbix 的强制性要求。如果 gettext 没有安装，前端也可以照常运行，但翻译将不可用。 |

# 第三步

请输入连接到数据库的详细信息。Zabbix 数据库必须提前创建完成。

![img](https://www.zabbix.com/documentation/4.0/_media/manual/installation/install_3.png?cache=)

# 第四步

请输入 Zabbix server 的详细信息。

![img](https://www.zabbix.com/documentation/4.0/_media/manual/installation/install_4.png?cache=)

# 第五步

查看设置的摘要。

![img](https://www.zabbix.com/documentation/4.0/_media/manual/installation/install_5.png?cache=)

# 第六步

下载配置文件并将其放在 conf/ 路径下，即在您复制 Zabbix PHP 文件的 Web 服务器 HTML 文档子目录中。

![img](https://www.zabbix.com/documentation/4.0/_media/manual/installation/install_6.png?cache=)

# 第七步

完成安装。

![img](https://www.zabbix.com/documentation/4.0/_media/manual/installation/install_7.png?cache=)

# 第八步

Zabbix 前端已经就绪！默认的用户名是Admin，密码是zabbix。

![login.png](https://www.zabbix.com/documentation/4.0/_media/manual/quickstart/login.png?cache=&w=384&h=427&tok=2bdf34)

# Zabbix监控服务器

1、创建主机

configure --> hosts --> Create host

![555749632682](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1555749632682.png)



![1555749866833](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1555749866833.png)

编译安装zabbix-agent

```bash
yum install -y vim iotop bc gcc gcc-c++ glibc glibc-devel pcre pcre-devel openssl openssl-devel zip unzip zlib-devel net-tools lrzsz tree ntpdate telnet lsof tcpdump wget libevent libevent-devel

useradd zabbix -s /sbin/nologin

tar xf zabbix-4.0.6.tar.gz

./configure --prefix=/usr/local/zabbix --enable-agent

make

make install

vim /etc/profile.d/zabbix.sh
export ZABBIX_HOME=/usr/local/zabbix
export PATH=$ZABBIX_HOME/bin:$ZABBIX_HOME/sbin:$PATH

source /etc/profile.d/zabbix.sh

cp /usr/local/src/zabbix-4.0.6/misc/init.d/fedora/core/zabbix_* /etc/init.d/

vim /usr/local/zabbix/etc/zabbix_agentd.conf
# Hostname=
Hostname=172.16.100.2
# Server=
Server=172.16.100.1

vim /etc/init.d/zabbix_server
vim /etc/init.d/zabbix_agent
# Zabbix-Directory
BASEDIR=/usr/local/zabbix
```

zabbix odbc数据库监控

zabbix 监控jmx

配置zabbix server选项

```bash
grep Java /usr/local/zabbix/etc/zabbix_server.conf | grep =
JavaGateway=127.0.0.1
JavaGatewayPort=10052
StartJavaPollers=5

service zabbix_agentd restart
```

安装tomcat

```bash
tar xf apache-tomcat-8.5.40.tar.gz

ln -s /usr/local/tomcat /usr/local/src/apache-tomcat-8.5.40

vim /etc/profile.d/tomcat.sh
export CATALINA_HOME=/usr/local/tomcat
export PATH=$CATALINA_HOME/bin:$PATH

export /etc/profile.d/tomcat.sh
```

配置tomcat

```javascript
vim /usr/local/tomcat/bin/catalina.sh
CATALINA_OPTS="$CATALINA_OPTS  -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=12345  -Dcom.sun.management.jmxremote.authenticate=false  -Dcom.sun.management.jmxremote.ssl=false  -Djava.rmi.server.hostname=172.16.100.1"
```

web端配置

![1555849692567](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1555849692567.png)

模板配置

![1555849777851](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1555849777851.png)

编译安装zabbix-agent



zabbix proxy被动模式

```bash
grep "^[a-Z]" /usr/local/zabbix/etc/zabbix_proxy.conf 
ProxyMode=1
Server=127.0.0.1
Hostname=Zabbix proxy
LogFile=/tmp/zabbix_proxy.log
DBHost=172.16.100.1
DBName=zabbix
DBUser=zabbix
DBPassword=123456
DBPort=3306
ProxyLocalBuffer=3
ProxyOfflineBuffer=24
HeartbeatFrequency=60
ConfigFrequency=60
DataSenderFrequency=5
StartPollers=20
JavaGateway=172.16.100.1
JavaGatewayPort=10052
StartJavaPollers=20
CacheSize=2G
HistoryCacheSize=2G
Timeout=3
Timeout=4
LogSlowQueries=3000
```

配置数据库

```mysql
create database zabbix_proxy character set utf8 collate utf8_bin;
grant all privileges on zabbix_proxy.* to zabbix@'172.16.100.1' identified by '123456';
flush privileges;
```

参数讲解

```
ProxyMode=1 #0为主动， 1为被动
Server=192.168.15.201 #zabbix server服务器的地址或主机名
Hostname=proxy1-mage-passive #代理服务器名称，需要与zabbix server添加代理时候的proxy name是一致的！
LogFile=/tmp/zabbix_proxy.log
DBHost=192.168.15.203 #数据库服务器地址
DBName=zabbix_proxy #使用的数据库名称
DBUser=proxy #连接数据库的用户名称
DBPassword=123456 #数据库用户密码
DBPort=3306 #数据库端口
ProxyLocalBuffer=3 #已经提交到zabbix server的数据保留时间
ProxyOfflineBuffer=24 #未提交到zabbix server的时间保留时间
HeartbeatFrequency=60 #心跳间隔检测时间， 默认60秒，范围0-3600秒，被动模式不使用
ConfigFrequency=5 #间隔多久从zabbix server 获取监控信息
DataSenderFrequency=5 #数据发送时间间隔，默认为1秒，范围为1-3600秒，被动模式不使用
StartPollers=20 #启动的数据采集器数量
JavaGateway=192.168.15.202 #java gateway服务器地址,当需要监控java的时候必须配置否则监控不到数据
JavaGatewayPort=10052 #Javagatewa服务端口
StartJavaPollers=20 #启动多少个线程采集数据
CacheSize=2G #保存监控项而占用的最大内存
HistoryCacheSize=2G #保存监控历史数据占用的最大内存
Timeout=30 #监控项超时时间，单位为秒
LogSlowQueries=3000 #毫秒，多久的数据库查询会被记录到日志
```

zabbix proxy主动模式

```bash
grep "^[a-Z]" /usr/local/zabbix/etc/zabbix_proxy.conf
ProxyMode=0 
Server=192.168.15.201 
Hostname=mage_proxy 
LogFile=/tmp/zabbix_proxy.log
DBHost=192.168.15.203 
DBName=zabbix_proxy
DBUser=zabbix
DBPassword=123456
DBPort=3306
ProxyLocalBuffer=3 
ProxyOfflineBuffer=24 
HeartbeatFrequency=60 
ConfigFrequency=5 
DataSenderFrequency=5 
StartPollers=20 
JavaGateway=192.168.15.202 
JavaGatewayPort=10052
StartJavaPollers=20 
CacheSize=2G 
HistoryCacheSize=2G 
Timeout=30
LogSlowQueries=3000
```

配置数据库

```mysql
create database zabbix_proxy_active character set utf8 collate utf8_bin;
grant all privileges on zabbix_proxy.* to zabbix@'172.16.100.1' identified by '123456';
flush privileges;
```

参数讲解

```
ProxyMode=0 #0为主动， 1为被动
Server=192.168.15.201 #zabbix server服务器的地址或主机名
Hostname=mage_proxy #代理服务器名称，需要与zabbix server添加代理时候的proxy name是一致的！
LogFile=/tmp/zabbix_proxy.log
DBHost=192.168.15.203 #数据库服务器地址
DBName=zabbix_proxy
DBUser=zabbix
DBPassword=123456
DBPort=3306
ProxyLocalBuffer=3 #已经提交到zabbix server的数据保留时间
ProxyOfflineBuffer=24 #未提交到zabbix server的时间保留时间
HeartbeatFrequency=60 #心跳间隔检测时间， 默认60秒，范围0-3600秒，被动模式不使用
ConfigFrequency=5 #间隔多久从zabbix server 获取监控信息
DataSenderFrequency=5 #数据发送时间间隔，默认为1秒，范围为1-3600秒，被动模式不使用
StartPollers=20 #启动的数据采集器数量
JavaGateway=192.168.15.202 #java gateway服务器地址
JavaGatewayPort=10052
StartJavaPollers=20 #启动多少个线程采集数据
CacheSize=2G #保存监控项而占用的最大内存
HistoryCacheSize=2G #保存监控历史数据占用的最大内存
Timeout=30
LogSlowQueries=3000
```

