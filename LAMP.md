LAMP：
	a：apache
	m：mariadb，mysql
	p：php，perl，python
	LAMP：memcached
	静态资源：静态内容；客户端从服务器获得的资源的表现心事与原文件相同；
	动态资源：通常是程序文件，需要在服务器上执行之后，将执行的结果返回给客户端；
		CGI：Common Gateway Interface
		fastcgi
		程序=指令+数据
	httpd+php：
		modules
		cgi
		fastcgi（fpm）
	请求过程：Client --> (http) --> (cgi) --> application server(progreams file) --> (mysql) --> mariadb
	CentOS 7:
		程序包：httpd，php，ph-mysql，mariadb-server
			注意：php要求httpd使用prefork MPM
		启动服务：
			systemctl start httpd.service
			systemctl start mariadb.Service
	测试
		php程序执行环境：
		测试php程序与mysql通信
Linux : CentOS Linux release 7.4.1708 (Core)(最小化安装)
Apache : httpd-2.4.33.tar.bz2
MariaDB : mariadb-10.2.14-linux-x86_64.tar.gz
PHP : php-7.1.17.tar.bz2
libmemcached : libmemcached-1.0.18.tar.gz
php-memcached : memcached-3.0.4.tgz
apr : apr-1.6.3.tar.gz
apr-util : apr-util-1.6.1.tar.gz
安装的顺序：
Apache --> MariaDB --> PHP
1.配置SELinux，防火墙
禁用SELinux

vim /etc/selinux/config

SELINUX=disabled

setenforce 0

配置防火墙放行http和https

firewall-cmd --permanent --zone=public --add-service=http

firewall-cmd --permanent --zone=public --add-service=https

firewall-cmd --reload

2.配置aliyun Yum源
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
3.安装开发工具组和依赖包

yum -y groupinstall "development tools" 

yum -y install pcre pcre-devel-devel openss-devel expat-devel

4.编译安装apr-1.4和apr-1.5
最新版的httpd2.4需要依赖apr-1.4以上版本和apr-1.5以上版本

tar vxf apr-1.6.3.tar.gz

cd apr-1.6.3

./configure --prefix=/usr/local/apr

make

make install

tar vxf apr-util-1.6.1.tar.gz

cd apr-util-1.6.1

./configure --prefix=/usr/local/apr-util --with-apr=/usr/local/apr

make

make install

5.编译安装httpd2.4.33

tar vxf httpd-2.4.33.tar.bz2

编译httpd之前把上一步解压出来的apr和apr-util文件复制到httpd-2.4.33/srclib/文件夹下，再进行httpd编译

mv apr-1.6.3 httpd-2.4.33/srclib/apr

mv apr-util-1.6.1 httpd-2.4.33/srclib/apr-util

cd httpd-2.4.33

./configure --prefix=/usr/local/apache --enable-so --enable-ssl --enable-cgi --enable-rewrite --with-zlib --with-pcre --with-included-apr --enable-modules=most --enable-mpms-shared=all --with-mpm=prefork

make

make install

配置以支持chkconfig控制

cp /usr/local/apache/bin/apachectl /etc/rc.d/init.d/httpd

vim /etc/rc.d/init.d/httpd

在第二行加入如下三行（前面的#号不能省略）

Comments to support chkconfig on RedHat Linux

chkconfig: 35 85 15

description: Apache is a World Wide Web server.

chkconfig --add httpd

chkconfig httpd on

systemctl restart httpd

vim /etc/profile.d/httpd.sh

export PATH=$PATH:/usr/local/apache/bin

source /etc/profile.d/httpd.sh

打开 http://192.168.10.10/ 可以看到httpd已经工作
6.编译安装Mariadb10.2.14
解压安装并指定解压路径/usr/local，创建软连接

tar xvf mariadb-10.2.14-linux-x86_64.tar.gz -C /usr/local

cd /usr/local

ln -s mariadb-10.2.14-linux-x86_64/ mysql

创建mysql用户，并指定家目录。

mkdir /mysql

useradd -r -m -s /sbin/nologin -d /mysql/data mysql

生成mysql数据库

/usr/local/mysql/scripts/mysql_install_db --datadir=/mysql/data --basedir=/usr/local/mysql --user=mysql

配置mysql的配置文件

cp /usr/local/mysql/support-files/my-huge.cnf /etc/my.cnf

vim /etc/my.cnf     ## 在[mysqld]节点下追加如下三行

[mysqld]
datadir = /mysql/data
innodb_file_per_table = ON
skip_name_resolve = ON

配置以支持chkconfig控制

cp /usr/local/mysql/support-files/mysql.server  /etc/init.d/mysqld

chkconfig --add mysqld

chkconfig mysqld on

systemctl restart mysqld

执行安全初始化数据库脚本

/usr/local/mysql/bin/mysql_secure_installation

vim /etc/profile.d/mysqld.sh

export PATH=$PATH:/usr/local/mysql/bin:/usr/local/apache/bin

source /etc/profile.d/mysqld.sh

7.编译安装PHP7.1.17
解决依赖

yum install -y epel-release

yum -y install php-mcrypt  libmcrypt  libmcrypt-devel libxml2-devel

编译安装

tar xvf php-7.1.17.tar.bz2

cd php-7.1.17

./configure --prefix=/usr/local/php --enable-mysqlnd --with-mysqli=mysqlnd --with-openssl --with-pdo-mysql=mysqlnd --enable-mbstring --with-freetype-dir --with-jpeg-dir --with-png-dir --with-zlib --with-libxml-dir=/usr --enable-xml --enable-sockets --with-apxs2=/usr/local/apache/bin/apxs --with-mcrypt --with-config-file-path=/etc --with-config-file-scan-dir=/etc/php.d --enable-maintainer-zts --disable-fileinfo

注意： php-7.0以上版本使用 –enable-mysqlnd –with-mysqli=mysqlnd ，原–with-mysql不再支持

make

make install

提供php配置文件

cp php.ini-production /etc/php.ini

编辑apache配置文件httpd.conf，以使apache支持php

vim /usr/local/apache/conf/httpd.conf

添加如下两行
AddType application/x-httpd-php .php
AddType application/x-httpd-php-source .phps
找到如下行并追加index.php 结果如下
DirectoryIndex index.html index.php

重启httpd服务以使配置生效

systemctl restart httpd

测试httpd、mariadb、php是否能够协同工作

rm -rf /usr/local/apache/htdocs/index.html

vim /usr/local/apache/htdocs/index.php

​    <?php
​        $mysqli=new mysqli("localhost","root","123456");
​        if(mysqli_connect_errno()){
​            echo "连接数据库失败!";
​            $mysqli=null;
​            exit;
​        }
​            echo "连接数据库成功!";
​            $mysqli->close();
​    ?>

打开http://192.168.10.10/ 能看到"连接数据库成功!"即可，否则请检查错误。
8.编译安装php-memcached扩展
最新版Xcache3.2最高只支持到PHP 5.6,所以PHP7不在支持列表里，我以这里我们使用php-memcached来提供缓存功能

编译安装libmemcached

yum -y install cyrus-sasl-devel

wget  https://launchpad.net/libmemcached/1.0/1.0.18/+download/libmemcached-1.0.18.tar.gz

cd libmemcached-1.0.18

./configure --prefix=/usr/local/libmemcached --with-memcached --enable-sasl

make

make install

编译安装php-memcached

tar xvf memcached-3.0.4.tgz

cd memcached-3.0.4

/usr/local/php/bin/phpize

​    Configuring for:
​    PHP Api Version:         20160303
​    Zend Module Api No:      20160303
​    Zend Extension Api No:   320160303

./configure --with-php-config=/usr/local/php/bin/php-config  --with-libmemcached-dir=/usr/local/libmemcached --enable-memcached

make 

make install

echo "extension=memcached.so" >> /etc/php.ini

systemctl restart httpd

附录：软件下载地址
CentOS 7.4.1708 : http://mirrors.sohu.com/centos/7/isos/x86_64/CentOS-7-x86_64-DVD-1708.iso
httpd-2.4.33.tar.bz2 : http://mirror.bit.edu.cn/apache//httpd/httpd-2.4.33.tar.bz2
mariadb-10.2.14-linux-x86_64.tar.gz : https://downloads.mariadb.org/interstitial/mariadb-10.2.14/bintar-linux-x86_64/mariadb-10.2.14-linux-x86_64.tar.gz
php-7.1.17.tar.bz2 : http://cn2.php.net/get/php-7.1.17.tar.bz2/from/this/mirror
libmemcached-1.0.18.tar.gz : https://launchpad.net/libmemcached/1.0/1.0.18/+download/libmemcached-1.0.18.tar.gz
memcached-3.0.4.tgz : http://pecl.php.net/get/memcached-3.0.4.tgz
apr-1.6.3.tar.gz : http://mirrors.shu.edu.cn/apache//apr/apr-1.6.3.tar.gz
apr-util-1.6.1.tar.gz : http://mirrors.shu.edu.cn/apache//apr/apr-util-1.6.1.tar.gz

LAMP+Redis：
	编译安装 redis：
		make prefix=/usr/local/redis install
		cp redis.conf /usr/local/redis