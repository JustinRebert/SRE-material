PXE:

 Preboot Execution Environment

 Client: 网卡要支持网络引导

 tftp: Trivial FTP, 简单文件传输协议, 高效传输小文件(udp:69)

 超级守护进程：xinetd
 	为那些极少接收用户请求的服务，专门提供监听功能
 		tftp: udp:69

​		瞬时(非独立)守护进程：它们无须定义在运行级别下，只需要一次性地定义xinetd的运行级别
 		独立(standalone)守护进程：能自我管理，无须xinetd提供监听服务的进程；

 	瞬时守护进程基于xinetd的配置文件：/etc/xinetd.d/Service_name

```
# chkconfig xinetd on
# service xinetd start
# chkconfig Service_name on
# service xinetd restart
```

 Linux上的tftp:
 	服务器：tftp-server
 	客户端：tftp

 配置步骤：
 前提：

```
# mkdir /media/cdrom
# mount -r /dev/dvd /media/cdrom
# vim /etc/yum.repos.d/media.repo
```

 1、配置DHCP服务器

```
yum -y install dhcp
vim /etc/dhcp/dhcpd.conf
```

 	自定义subnet：

```
 subnet {
 	...
 	next-server TFTP-SERVER-IP;
 	filename "pxelinux.0";
 }
service dhcpd restart
tail -f /var/log/boot.log
```

 2、配置tftp-server
```
# yum -y install xinetd tftp-server tftp
# chkconfig xinetd on
# chkconfig tftp on
# service xinetd start
# ss -unl | grep ":69"
```

 3、准备安装树
```
# mkdir /var/www/html/centos6
# mount --bind /media/cdrom /var/www/html/centos6
# service httpd start
```

 4、准备tftpboot下的文件
```
# yum -y install syslinux
# cp /media/cdrom/images/pxeboot/{vmlinuz,initrd.img}  /var/lib/tftpboot/
# cp /media/cdrom/isolinux/{boot.msg,vesamenu.c32,splash.jpg}  /var/lib/tftpboot/
# cp /usr/share/syslinux/pxelinux.0  /var/lib/tftpboot/
# mkdir /var/lib/tftpboot/pxelinux.cfg
# cp /media/cdrom/isolinux/isolinux.cfg  /var/lib/tftpboot/pxelinux.cfg/default
```

 5、第一次测试

 6、提供kickstart文件
 	注意：url及repo后的路径要修改为可用安装树的路径；
 	编辑好kickstart文件后保存至/var/www/html目录下；这里假设为ks.cfg

 7、配置引导程序能自动加载此kickstart文件
 	编辑/var/lib/tftpboot/pxelinux.cfg/default
 	在label为linux项的append一行后附加：
 		ks=http://HTTP_SERVER_IP/ks.cfg

 8、第二次测试


SELinux: Secure Enhenced Linux

获取selinux的当前状态：
```
getenforce
```

临时启用或禁用：

```
setenfoce 0|1
```

启用：/etc/sysconfig/selinux
	  /etc/selinux/config
	  找到其中的一项：

```
SELINUX={enforcing|permissive|disabled}
```

