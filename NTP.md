一、NTP简介

在计算机的世界里，时间非常地重要，例如对于火箭发射这种科研活动，对时间的统一性和准确性要求就非常地高，是按照A这台计算机的时间，还是按照B这台计算机的时间？NTP就是用来解决这个问题的，NTP（Network Time Protocol，网络时间协议）是用来使网络中的各个计算机时间同步的一种协议。它的用途是把计算机的时钟同步到世界协调时UTC，其精度在局域网内可达0.1ms，在互联网上绝大多数的地方其精度可以达到1-50ms。

它可以使计算机对其服务器或时钟源（如石英钟，GPS等等）进行时间同步，它可以提供高精准度的时间校正，而且可以使用加密确认的方式来防止恶毒的协议攻击。

二、环境准备

Centos6.5 x86_64 192.168.44.130

三、安装NTP

1).首先查看NTP是否安装

```bash
[root@soysauce ~]# rpm -q ntp
package ntp is not installed
```

2).未安装则现在安装ntp(如果已安装则跳过此步)

```bash
[root@soysauce ~]# yum install -y ntp
```

四、配置NTP

1).主配置文件/etc/ntp.conf

在ntp.conf 档案内可以利用『 restrict 』来控管权限，这个参数的设定方式为：

 restrict [your_ip] mask [netmask_IP] [parameter]

其中parameter 的参数主要有底下这些：

- ignore：拒绝所有类型的NTP联机；
- nomodify：客户端不能使用ntpc与ntpq这两支程序来修改服务器的时间参数，但客户端仍可透过这部主机来进行网络校时的；
- noquery：客户端不能够使用ntpq, ntpc等指令来查询时间服务器，等于不提供NTP的网络校时啰；
- notrap：不提供trap这个远程事件登录(remote event logging)的功能。
- notrust：拒绝没有认证的客户端。

那如果你没有在parameter的地方加上任何参数的话，这表示『该IP或网段不受任何限制』的意思喔！一般来说，我们可以先关闭NTP的权限，然后再一个一个的启用允许登入的网段。

2).修改主配置文件/etc/ntp.conf,添加如下一行

example: 

```bash
[root@soysauce ~]# vim /etc/ntp.conf
restrict 192.168.44.130 mask 255.255.255.0 nomodify notrap
```

五、启动NTP

1).启动服务

```bash
[root@soysauce ~]# service ntpd start
Starting ntpd:                                             [  OK  ]
```

2).加入开机服务列表中

```bash
[root@soysauce ~]# chkconfig ntpd on
```

3).添加防火墙规则

```bash
[root@soysauce ~]# vim /etc/sysconfig/iptables
```

-A INPUT -d 192.168.44.130 -p udp --dport 123 -m state --state NEW -j ACCEPT

4).查看udp123端口是否处于监听状态

```bash
[root@soysauce ~]# ss -unlp|grep "ntpd"
State      Recv-Q Send-Q                            Local Address:Port                              Peer Address:Port 
UNCONN     0      0                                 192.168.44.130:123                                          *:*      users:(("ntpd",23537,18))
UNCONN     0      0                                     127.0.0.1:123                                          *:*      users:(("ntpd",23537,17))
UNCONN     0      0                                             *:123                                          *:*      users:(("ntpd",23537,16))
```

5).查看NTP服务状态

```bash
[root@soysauce ~]# ntpstat 
synchronised to NTP server (202.118.1.130) at stratum 3 
   time correct to within 45 ms
   polling server every 128 s
```

六、客户端同步

1).客户端手动同步

```bash
[root@NetworkService-3 ~]# ntpdate 192.168.44.130
29 Sep 22:59:31 ntpdate[5323]: adjust time server 192.168.44.130 offset 0.034930 sec
```

2).添加crontab任务计划

```bash
[root@NetworkService-3 ~]# crontab -e
# update time from 192.168.44.130 one hour
* */1 * * * ntpdate 192.168.44.130 &> /var/log/ntpdate.log
```