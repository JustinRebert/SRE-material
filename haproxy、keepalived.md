1.haproxy 对于后端服务器的三种状态监测方式

第一种：check

check 默认基于端口检测；java:端口存在但服务不一定生效

```
server 172.168.112.17  172.168.112.17:80 check  port 9000 address SERVER_IP inter 3000 fall 3 rise 5 weight 1
```

第二种：基于指定URI

```
# option httpchk GET /index.html HTTP/1.0
/index.html=/apps/nginx/html/index.html；
```

此处 /index.html 与开发协商好的不可变得的绝对路径.

#tail -f /apps/nginx/logs/access_json.log



/apps/nginx/html/monitor/monitor.html

第三种：通过request获取的头部信息进行匹配

option httpchk HEAD /index.html HTTP/1.0\r\nHost:\ 192.168.112.27(负载均衡IP)

2.haproxy 自定义错误页面的两种方式 注意：nginx避免报原生错误页面也需要自定义

示例：

```
# mkdir /usr/local/haproxy/html/
# echo 500 > /usr/local/haproxy/html/500.html
...
# echo 503 > /usr/local/haproxy/html/500.html
# vim /etc/haproxy/haproxy.cfg
defaults
errorfile 500 /usr/local/haproxy/html/500.html #自定义错误页面跳转
errorfile 502 /usr/local/haproxy/html/502.html
errorfile 503 /usr/local/haproxy/html/503.html
#errorloc 503 http://192.168.112.7/error_page/503.html 自定义错误跳转
listen web_server
bind 192.168.112.27:80
mode http
acl test_host hdr_dom(host) .magedu.net
use_backend test_host_server if test_host
default-backend default_web
backend test_host_server
server 172.168.112.27  172.168.112.27:80 cookie web1 check inter 3s fall 3 rise 5 weight 1
backend default_web
server 127.0.0.1 127.0.0.1:80 cookie web1 check inter 3s fall 3 rise 5 weight 1
192.168.112.7
# mkdir /apps/nginx/html/error_page/
# echo 503 > /apps/nginx/html//error_page/503.html
```



3.haproxy实现基于ACL实现域名访问控制 #工作在七层

最好使用frontend；

#基于域名匹配匹配，如果www.magedu.net匹配host，表示为true执行。

```
acl test_host hdr_dom(host) www.magedu.net
#test_host匹配后定义test_host_server并执行。test_host_server为后端服务器
use_backend test_host_server if test_host
```

#以上都没有匹配到的时候使用默认backend

default_backend default_web

示例一：

```
listen web-server
bind 172.168.112.17:80
mode http
acl test_host hdr_dom(host) www.magedu.net
use_backend test_host_server if test_host
default_backend default_web
backend test_host_server
#option httpchk HEAD /index.html HTTP/1.0\r\nHost:\ 192.168.112.27
#cookie SERVER-COOKIE insert indirect nocache
#server 172.168.112.17  172.168.112.17:80 cookie web1 check inter 3s fall 3 rise 5 weight 1
server 192.168.112.7 www.magedu.com:80 check inter 3s fall 3 rise 5 weight 1
backend default_web
server 127.0.0.1 127.0.0.1:80 check inter 3s fall 3 rise 5 weight 1
```



示例二：

```
listen web-server
bind 172.168.112.27:80
mode http
======================ACL============================
acl test_host hdr_dom(host) (www.magedu.net www.mageedu.net) | (.magedu.net)#此种多数应用早接口EPI上
acl mobilie_test hdr_dom(host) (mobile.magedu.net) | (.mageedu.net)
=====================Use ACL=========================
use_backend test_host_server if test_host
use_backend mobile_host_server if mobilie_test
===================Default_backend=====================
default_backend default_web
backend test_host_server
server 172.168.112.27 www.magedu.com:80 check inter 3s fall 3 rise 5 weight 1
backend mobile_host_server
server 192.168.112.7 192.168.112.7:80 check inter 3s fall 3 rise 5 weight 1
backend default_web
server 127.0.0.1 127.0.0.1:80 check inter 3s fall 3 rise 5 weight 1
```

4.keepalived 单播配置

单播配置：

 unicast_src_ip 本机源IP

  	  unicast_peer {

  	    目标主机IP

 	   } 

vrrp_strict #严格遵守VRRP协议,不允许状况:1,没有VIP地址,2.有单播邻居,3.在VRRP版本2中有IPv6地址



编译安装keepalived-2.0.12

```
# yum install gcc gcc-c++ openssl openssl-devel libnl3-devel libnfnetlink-devel zlib-devel file-devel net-snmp-devel glib2-devel json-c-devel pcre2-devel  libnftnl-devel libmnl-devel lrzsz ntpdate net-tools tcpdump -y
# cd /usr/local/src
# tar xf keepalived-2.0.12.tar.gz
# cd keepalived-2.0.12
# ./configure --prefix=/usr/local/keepalived
# make && make install
# mkdir /etc/keepalived
# cd /usr/local/src/keepalived-2.0.12/keepalived/etc
# cp init.d/keepalived /etc/init.d/
# cp sysconfig/keepalived /etc/sysconfig/
# cp keepalived/keepalived.conf /etc/keepalived/
##cp /usr/local/src/keepalived-2.0.12/bin/keepalived /usr/bin/
```

192. 168.112.7BACKUP

```
vim /etc/keepalived/keepalived.conf

! Configuration File for keepalived
global_defs {
   notification_email {
    root@localhost
   }
   notification_email_from keepalived@localhost
   smtp_server 127.0.0.1
   smtp_connect_timeout 3
   router_id LVS_DEVEL
   vrrp_skip_check_adv_addr
   #vrrp_strict
   #vrrp_garp_interval 0
   #vrrp_gna_interval 0
}
vrrp_instance VI_1 {
    state BACKUP
    interface eth0
    virtual_router_id 52
    priority 60
    advert_int 1
    unicast_src_ip 172.18.6.2
    unicast_peer {
      192.168.112.37
    }
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
      192.168.112.27 dev eth0 label eth0:0
    }
}
```



192.168.112.37 MASTER

```
! Configuration File for keepalived
global_defs {
   notification_email {
    root@localhost
   }
   notification_email_from keepalived@localhost
   smtp_server 127.0.0.1
   smtp_connect_timeout 3
   router_id LVS_DEVEL
   vrrp_skip_check_adv_addr
   #vrrp_strict
   #vrrp_garp_interval 0
   #vrrp_gna_interval 0
}

vrrp_instance VI_1 {
    state MASTER
    interface eth0
    virtual_router_id 52
    priority 80
    advert_int 1
    unicast_src_ip 192.168.112.37
    unicast_peer {
      172.18.6.2
    } 
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
      192.168.112.27 dev eth0 label eth0:0
    }
}
```



测试单播：# tcpdump -i eth0 -nn host 192.168.112.27 and host 192.168.112.37



多个VIP

```
# vim /etc/keepalived/keepalived.conf
! Configuration File for keepalived
global_defs {
  notification_email {
    root@localhost
   }
   notification_email_from keepalived@localhost
   smtp_server 127.0.0.1
   smtp_connect_timeout 3
   router_id LVS_DEVEL
   vrrp_skip_check_adv_addr
   #vrrp_strict #组播
   #vrrp_garp_interval 0 #ARP报文发送延迟
   #vrrp_gna_interval 0 #消息发送延迟
   #vrrp_mcast_group4 239.239.239.239 #组播IP地址， 224.0.0.0到239.255.255.255
}
vrrp_instance VI_1 {
    state MASTER
    interface eth0
    virtual_router_id 51
    priority 80
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
      172.18.6.67 dev eth0 label eth0:1
      172.18.6.87 dev eth0 label eth0:2
    }
}
vrrp_instance VI_2 {
    state BACKUP
    interface eth0
    virtual_router_id 52
    priority 60
    advert_int 1
    unicast_src_ip 172.18.6.2
    unicast_peer {
      192.168.112.37
    } # 组播启用时，此处单播不可有
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
      192.168.112.27 dev eth0 label eth0:0
    }
}
```



测试组播：# tcpdump -i eth0 -nn host 239.239.239.239



5.三主机配置keepalived VIP高可用

配置参数：

state MASTER|BACKUP：当前节点在此虚拟路由器上的初始状态；只能有一个是MASTER，余下的都应该为BACKUP。

interface IFACE_NAME：绑定为当前虚拟路由器使用的物理接口 ens32,eth0,bond0,br0。

virtual_router_id VRID：当前虚拟路由器惟一标识，范围是0-255。

priority 100：当前物理节点在此虚拟路由器中的优先级；范围1-254。

advert_int 1： vrrp通告的时间间隔，默认1s。



配置示例： 配置priority有效  配置MASTER和BACKUP无效并不区分

192. 168.112.7 MASTER

```
vrrp_instance VI_1 {
    state MASTER
    interface eth0
    virtual_router_id 52
    priority 80
    advert_int 1
    preempt_delay
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.112.27 dev eth0 label eth0:0
    }
}
```

192. 168.112.27 BACKUP

```
vrrp_instance VI_1 {
    state BACKUP
    interface eth0
    virtual_router_id 52
    priority 60
    advert_int 1
   #nopreempt
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.112.27 dev eth0 label eth0:0
    }
}
```



192.168.112.37 BACKUP

```
vrrp_instance VI_1 {
    state BACKUP
    interface eth0
    virtual_router_id 52
    priority 40
    advert_int 1
  #nopreempt
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.112.27 dev eth0 label eth0:0
    }
}
```



Keepalived通知配置：

# yum install -y mailx

Keepalived通知脚本：

```
# vim /etc/keepalived/notify.sh
#!/bin/bash
contact='123456@qq.com'
notify() {
  mailsubject="$(hostname) to be $1, vip 转移"
  mailbody="$(date +'%F %T'):vrrp transition,$(hostname) changed to be $1"
  echo "$mailbody" | mail -s "$mailsubject" $contact
}
case $1 in
master)
  notify master
  ;;
backup)
  notify backup
  ;;
fault)
  notify fault
  ;;
*)
  echo "Usage: $(basename $0) {master|backup|fault}"
  exit 1
  ;;
```

esac

脚本的调用方法：

notify_master "/etc/keepalived/notify.sh master"

notify_backup "/etc/keepalived/notify.sh backup"

notify_fault "/etc/keepalived/notify.sh fault"







(keepalived+haproxy)+nginx

# vim /etc/sysctl.conf #(keepalived+haproxy)两台主机执行

net.ipv4.ip_nonlocal_bind = 1



环境：

192.168.112.27  (keepalived(MASTER)+haproxy)

192.168.112.37  (keepalived(BACKUP)+haproxy)

172.168.112.27 nginx-web1 

192.168.112.7 nginx-web2



```
#vim /etc/keepalived/keepalived.conf #两个高可用配置文件注意主从关系
global_defs {
   notification_email {
    root@localhost
   }
   notification_email_from keepalived@localhost
   smtp_server 127.0.0.1
   smtp_connect_timeout 3
   router_id LVS_DEVEL
   vrrp_skip_check_adv_addr
  # vrrp_strict
  # vrrp_garp_interval 1
  # vrrp_gna_interval 1
  # vrrp_mcast_group4 239.239.239.239
}
vrrp_instance VI_1 {
    state MASTER
    interface eth0
    virtual_router_id 52
    priority 100
    advert_int 1
    #preempt_delay
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
      192.168.112.27 dev eth0 label eth0:0 #VIP
    }
}
```



```
# vim /etc/haproxy/haproxy.cfg #两个负载配置文件相同
global
  uid        997
  gid        995
  daemon
  nbproc     1
  maxconn    100000
  log        127.0.0.1 local2 info
  pidfile    /usr/local/haproxy/run/haproxy.pid
defaults
  mode      http
  maxconn   100000
  option    forwardfor
  option    http-keep-alive
  option    redispatch
  option    abortonclose
  timeout connect    120s
  timeout client     300s
  timeout server     600s
  timeout http-keep-alive 120s
listen stats
  bind :9009
  stats enable
  stats hide-version
  stats uri /haproxy-status
  stats realm HAPorxy\ Stats\ Page
  stats auth haadmin:123456
  stats auth admin:123456
  stats refresh 30s
listen web_port_80
  bind   192.168.112.27:80 #VIP
  mode   http
  option forwardfor
  server 172.168.112.27  172.168.112.27:80 check inter 3s fall 3 rise 5
  server 192.168.112.7 192.168.112.7:80 check inter 3s fall 3 rise 5
```

