Mariadb：
	（1）源码安装：
		准备依赖包：cmake gcc readline-devel zlib-devel openssl-devel libaio-devel gcc-c++ libxml2 libxml2-devel git bison bison-devel
		源码：
			下载源码包：(https://downloads.mariadb.org/interstitial/mariadb-10.3.13/source/mariadb-10.3.13.tar.gz/from/http%3A//ftp.yz.yamagata-u.ac.jp/pub/dbms/mariadb/)
		配置：
			cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_DATADIR=/data/mysql -DSYSCONFDIR=/etc -DWITHOUT_TOKUDB=1 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STPRAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWIYH_READLINE=1 -DWIYH_SSL=system -DVITH_ZLIB=system -DWITH_LOBWRAP=0 -DMYSQL_UNIX_ADDR=/tmp/mysql.sock -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci
		编译：
			make
		安装：
			make install
		注意：如果cmake出错，删除CmakeCache.txt
	（2）将源码安装的MySQL添加为系统服务
		cp support-files/mysqld.server /etc/init.d/mysqld
		chkconfig --add mysqld
		chkconfig --level 35 mysqld on
		mkdir -p /data/mysql
		useradd –r –s /sbin/nologin –d /data/mysql/ mysql
		/usr/local/mysql/scripts/mysql_install_db --user=mysql --datadir=/data/mysql/ --basedir=/usr/local/mysql
		ln -sv /usr/local/mysql/bin/* /usr/bin/
	（3）修改/etc/my.cnf：
		datadir=/data/mysql/
		socket=/tmp/mysql.sock

二进制安装mariadb：

（1）

