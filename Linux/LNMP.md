1.源码编译实现LNMP环境示例主要步骤：

（1）安装php相关依赖包

```bash
 dnf -y install wget vim pcre pcre-devel openssl openssl-devel libicu-devel gcc gcc-c++ autoconf libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel glibc glibc-devel glib2 glib2-devel ncurses ncurses-devel curl curl-devel krb5-devel libidn libidn-devel openldap openldap-devel nss_ldap jemalloc-devel cmake boost-devel bison automake libevent libevent-devel gd gd-devel libtool* libmcrypt libmcrypt-devel mcrypt mhash libxslt libxslt-devel readline readline-devel gmp gmp-devel libcurl libcurl-devel openjpeg-devel
```

（2）下载源代码：

```bash
 wget http://120.52.51.16/cn2.php.net/distributions/php-7.3.3.tar.gz
```

（3）配置参数：

```bash
 ./configure --prefix=/apps/php --enable-fpm --with-fpm-user=www --with-fpm-group=www --with-pear --with-curl --with-png-dir --with-freetype-dir --with-iconv --with-mhash --with-zlib --with-xmlrpc --with-xsl --with-openssl --with-mysqli --with-pdo-mysql --disable-debug --enable-zip --enable-sockets --enable-soap --enable-inline-optimization --enable-xml --enable-ftp --enable-exif --enable-wddx --enable-bcmath --enable-calendar --enable-shmop --enable-dba --enable-sysvsem --enable-sysvshm --enable-sysvmsg
```

(4)编译并安装：

```bash
  make && make install
```

（5）修改并配置：

```bash
 cp www.conf.default www.conf
```

```bash
 cp /usr/local/src/php-7.3.3/php.ini-production /apps/php/etc/php.ini
```

```bash
grep -v ";" /apps/php/etc/php-fpm.d/www.conf | grep -v "^$"
	[www]
	user = www
	group = www
	listen = 127.0.0.1:9000
	listen.allowed_clients = 127.0.0.1
	pm = dynamic
	pm.max_children = 50
	pm.start_servers = 30
	pm.min_spare_servers = 30
	pm.max_spare_servers = 35
	pm.status_path = /pm_status
	ping.path = /ping
	ping.response = pong
	access.log = log/$pool.access.log
	slowlog = log/$pool.log.slow
```

（5）检查配置文件并启动php

```bash
cp php-fpm.conf.default php-fpm.conf	
```

```bash
/apps/php/sbin/php-fpm -c /apps/php/etc/php.ini
```

（6）安装mysql相关依赖包：

```bash
 dnf install vim gcc gcc-c++ wget autoconf net-tools lrzsz iotop lsof iotop bash-completion curl policycoreutils openssh-server openssh-clients postfix -y
```

（7）获取mysql二进制安装包

```bash
 wget https://cdn.mysql.com//Downloads/MySQL-5.6/mysql-5.6.43-linux-glibc2.12-x86_64.tar.gz
```

（8）配置	

```bash
 ln -sv /usr/local/src/mysql-5.6.43-linux-glibc2.12-x86_64 /usr/local/mysql
 useradd -s /sbin/nologin mysql
 mkdir -pv /data/mysql /var/lib/mysql
 chown -R mysql.mysql /data/mysql/ /var/lib/mysql/
 /usr/local/mysql/scripts/mysql_install_db --user=mysql --datadir=/data/mysql --basedir=/usr/local/mysql
 cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld	
 chmod a+x /etc/init.d/mysqld 	
 grep -v "#" /etc/my.cnf | grep -v "^$"
[mysqld]
datadir=/data/mysql
socket=/data/mysql/mysql.sock
user=mysql
symbolic-links=0
skip-name-resolve=on
innodb_file_per_table=1
max_connections=10000
[client]
port=3306
socket=/var/lib/mysql/mysql.socket
[mysqld_safe]
log-error=/var/log/mysql.log
pid-file=/tmp/mysql.sock
!includedir /etc/my.cnf.d
```

（10）启动数据库服务	

```bash
 /etc/init.d/mysqld start
```

（11）安装nginx'依赖包：

```bash
 dnf install -y vim lrzsz tree screen psmisc lsof tcpdump wget ntpdate gcc gcc-c++ glibc glibc-devel pcre pcre-devel openssllib-devel bash-completion nfs-utils automake libxml2 libxml2-devel libxslt libxslt-devel perl perl-ExtUtils-Embed
```

（12）获取nginx源代码：	

```bash
 wget https://nginx.org/download/nginx-1.12.2.tar.gz
```

（13）配置参数：

```bash
 ./configure --prefix=/apps/nginx  --user=www  --group=www  --with-http_ssl_module  --with-http_v2_module  --with-http_realistatic_module  --with-pcre  --with-stream  --with-stream_ssl_module  --with-stream_realip_module
```

（15）编译并安装：

```bash
 make && make install
```

（16）nginx配置

```bash
  grep -v "#" /apps/nginx/conf/nginx.conf | grep -v "^$"
worker_processes  1;
	events {
		worker_connections  1024;
	}
	http {
		include       mime.types;
		default_type  application/octet-stream;
		sendfile        on;
		keepalive_timeout  65;
		server {
			listen       80;
			server_name  www.magedu.net;
			location / {
				root   /data/nginx/wordpress;
				index  index.php index.html index.htm;
				if ($http_user_agent ~ "ApacheBench|WebBench|TurnitinBot|Sogou web spider|Grid Service") {
					return 403;
			}
		}
		location ~ \.php$ {
			root /data/nginx/wordpress;
			fastcgi_pass 127.0.0.1:9000;
			fastcgi_index index.php;
			fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
			include fastcgi_params;
		}
		error_page   500 502 503 504  /50x.html;
		location = /50x.html {
			root   html;
		}
	}
}
```

17）检查配置文件并启动服务

```bash
 /apps/nginx/sbin/nginx -t
 /apps/nginx/sbin/nginx
 cat /data/nginx/wordpress/test.php 
<?php
	$dsn='mysql:host=192.168.112.17;dbname=wordpress';
	$username='wordpress';
	$passwd='123456';
	$dbh=new PDO($dsn,$username,$passwd);
	var_dump($dbh);
?>
```

（18）测试

```bash
 curl www.magedu.net/test.php
	object(PDO)#1 (0) {
}
```

