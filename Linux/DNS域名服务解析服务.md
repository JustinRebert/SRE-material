# DNS服务介绍

## DNS服务简介：

DNS(Domain Name System–域名系统),是因特网的一项服务。它作为将域名和IP地址相互映射的一个分布式数据库，能够使人更方便地访问互联网。是一个应用层的协议DNS使用TCP和UDP端口53。

DNS是一个分布式数据库,命名系统采用层次的逻辑结构,如同一颗倒置的树,这个逻辑的树形结构称为域名空间,由于DNS划分了域名空间,所以各机构可以使用自己的域名空间创建DNS信息.

> 注:DNS域名空间中,树的最大深度不得超过127层,树中每个节点最长可以存储63个字符.

## DNS名词解释：

### 1. 域和域名

DNS树的每个节点代表一个域.通过这些节点,对整个域名空间进行划分,成为一个层次结构.

域名空间的每个域的名字,通过域名进行表示.

域名:通常由一个完全合格域名（FQDN）标识.FQDN能准确表示出其相对于DNS 域树根的位置,也就是节点到DNS 树根的完整表述方式,从节点到树根采用反向书写,并将每个节点用“.”分隔,对于DNS 域google 来说,其完全正式域名（FQDN）为google.com.

例如：google为com域的子域,其表示方法为google.com，而www为google域中的子域,可以使用www.google.com表示

> 注意:通常,FQDN 有严格的命名限制,长度不能超过256 字节,只允许使用字符a-z,0-9,A-Z和减号（-）.点号（.）只允许在域名标志之间（例如“google.com”）或者FQDN 的结尾使用.

域名不区分大小.

由最顶层到下层,可以分成:根域、顶级域、二级域、子域.

Internet 域名空间的最顶层是根域（root）,其记录着Internet 的重要DNS 信息,由Internet域名注册授权机构管理,该机构把域名空间各部分的管理责任分配给连接到Internet 的各个组织.

“.”全球有13个根(root)服务器

DNS 根域下面是顶级域,也由Internet 域名注册授权机构管理.共有3 种类型的顶级域.

组织域:采用3 个字符的代号,表示DNS 域中所包含的组织的主要功能或活动.比如com 为商业机构组织,edu 为教育机构组织,gov 为政府机构组织,mil 为军事机构组织,net 为网络机构组织,org 为非营利机构组织,int 为国际机构组织.

地址域:采用两个字符的国家或地区代号.如cn 为中国,kr 为韩国,us 为美国.

反向域:这是个特殊域,名字为in-addr.arpa,用于将IP 地址映射到名字（反向查询）.

对于顶级域的下级域,Internet 域名注册授权机构授权给Internet 的各种组织.当一个组织获得了对域名空间某一部分的授权后,该组织就负责命名所分配的域及其子域,包括域中的计算机和其他设备,并管理分配域中主机名与IP 地址的映射信息.

### 2、区（Zone）：

区是DNS 名称空间的一部分,其包含了一组存储在DNS 服务器上的资源记录.

使用区的概念,DNS 服务器回答关于自己区中主机的查询,每个区都有自己的授权服务器.

### 3.主域名服务器和辅助域名服务器：

当区的辅助服务器启动时,它与该区的主控服务器进行连接并启动一次区传输,区辅助服务器定期与区主控服务器通信,查看区数据是否改变.如果改变了,它就启动一次数据更新传输.每个区必须有主服务器,另外每个区至少要有一台辅助服务器,否则如果该区的主服务器崩溃了,就无法解析该区的名称.

辅助服务器的优点:

1. 容错能力

    配置辅助服务器后,在该区主服务器崩溃的情况下,客户机仍能解析该区的名称.一般把区的主服务器和区的辅助服务器安装在不同子网上,这样如果到一个子网的连接中断,DNS 客户机还能直接查询另一个子网上的名称服务器.

2. 减少广域链路的通信量

    如果某个区在远程有大量客户机,用户就可以在远程添加该区的辅助服务器,并把远程的客户机配置成先查询这些服务器,这样就能防止远程客户机通过慢速链路通信来进行DNS 查询.

3. 减轻主服务器的负载

    辅助服务器能回答该区的查询,从而减少该区主服务器必须回答的查询数.

### 4.DNS相关概念：

- DNS服务器：

    运行DNS 服务器程序的计算机,储存DNS 数据库信息.DNS 服务器会尝试解析客户机的查询请求.

    在解答查询时,如果DNS 服务器能提供所请求的信息,就直接回应解析结果,如果该DNS 服务器没有相应的域名信息,则为客户机提供另一个能帮助解析查询的服务器地址,如果以上两种方法均失败,则回应客户机没有所请求的信息或请求的信息不存在.

- DNS缓存：

    运行DNS 服务器程序的计算机,储存DNS 数据库信息.DNS 服务器会尝试解析客户机的查询请求.

    在解答查询时,如果DNS 服务器能提供所请求的信息,就直接回应解析结果,如果该DNS 服务器没有相应的域名信息,则为客户机提供另一个能帮助解析查询的服务器地址,如果以上两种方法均失败,则回应客户机没有所请求的信息或请求的信息不存在.

### 5、DNS两种查询方式：

1. 递归查询：

    递归查询是一种DNS 服务器的查询模式,在该模式下DNS 服务器接收到客户机请求,必须使用一个准确的查询结果回复客户机.如果DNS 服务器本地没有存储查询DNS 信息,那么该服务器会询问其他服务器,并将返回的查询结果提交给客户机.

2. 迭代查询：

    DNS 服务器另外一种查询方式为迭代查询,当客户机发送查询请求时,DNS 服务器并不直接回复查询结果,而是告诉客户机另一台DNS 服务器地址,客户机再向这台DNS 服务器提交请求,依次循环直到返回查询的结果为止.

### 6、正向解析和方向解析：

- 正向解析：是指域名到IP地址的解析过程。
- 反向解析：是指IP地址到域名的解析过程。

### 7、DNS资源记录：

1. SOA 资源记录(全区唯一)

    每个区在区的开始处都包含了一个起始授权记录（Start of Authority Record）,简称SOA 记录.

    SOA 定义了域的全局参数,进行整个域的管理设置.一个区域文件只允许存在唯一的SOA 记录.

2. NS 资源记录:

    NS（Name Server）记录是域名服务器记录,用来指定该域名由哪个DNS服务器来进行解析.每个区在区根处至少包含一个NS 记录.

3. A 资源记录

    地址（A）资源记录把FQDN 映射到IP 地址. 因为有此记录,所以DNS服务器能解析FQDN域名对应的IP 地址.

    A ：是IPv4地址。 AAAA是IPv6主机地址。

4. PTR 资源记录

    相对于A 资源记录,指针（PTR）记录把IP地址映射到FQDN. 用于反向查询,通过IP地址,找到域名.

5. CNAME 资源记录

    别名记录（CNAME）资源记录创建特定FQDN 的别名.用户可以使用CNAME 记录来隐藏用户网络的实现细节,使连接的客户机无法知道真正的域名.

    例:ping百度时,解析到了百度的别名服务器.百度有个cname=[www.a.shifen.com.的别名](http://www.a.shifen.com.xn--mcrt9bhy5d/)

6. MX 资源记录

    邮件交换（MX）资源记录,为DNS 域名指定邮件交换服务器.

    邮件交换服务器是为DNS 域名处理或转发邮件的主机.处理邮件指把邮件投递到目的地或转交另一不同类型的邮件传送者.转发邮件指把邮件发送到最终目的服务器,用简单邮件传输协议SMTP 把邮件发送给离最终目的地最近的邮件交换服务器,或使邮件经过一定时间的排队.

## DNS工作原理：查询过程

假设www.abc.com的主机要查询www.xyz.abc.com的服务器ip地址。

### 递归查询：

第一步：在hosts静态文件、DNS解析器缓存中查找某主机的ip地址

> hosts文件：以静态映射的方式提供IP地址与主机名的对照表，类似ARP表
>
> 域：abc.com是一个域，它可以划分为多个区域，如abc.com和xyz.abc.com

第二步：上一步无法找到，去DNS本地服务器（即域服务器）查找，其本质是去区域服务器、服务器缓存中查找

第三步：本地DNS服务器查不到就根据‘根提示文件’向负责顶级域‘.com’的DNS服务器查询

第四步：‘根DNS服务器’根据查询域名中的‘xyz.com’，再向xyz.com的区域服务器查询

第五步：www.xyz.abc.com的DNS服务器直接解析该域名，将查询到的ip再原路返回给请求查询的主机]

### 迭代查询

第一步：在hosts静态文件、DNS解析器缓存中查找某主机的ip地址

第二步：上一步无法找到，在DNS本地服务器（即域服务器）查找所有本层次的区域服务器

第三步：本地DNS服务器查不到就查询上一层次的所有区域服务器，以此类推直至根域名DNS服务器‘.’

第四步：到达根域名服务器后又向下查询，直至查到结果为止。

### 迭代查询与递归查询结合

递归查询需要经过逐层查询才能获得查询结果，当查询具有许多层次的DNS结构时效率很低，所以一般采用两者相结合的查询方式。

第一步：在hosts静态文件、DNS解析器缓存中查找某主机的ip地址

第二步：上一步无法找到，去DNS本地服务器（即域服务器）查找，其本质是去区域服务器、服务器缓存中查找

第三步：本地DNS服务器查不到就根据‘根提示文件’向负责顶级域‘.com’的根DNS服务器查询

第四步：根DNS服务器直接将其区域DNS服务器的ip地址返回给本地服务器，而不用再向xyz.com的区域服务器查询。

第五步：本地DNS服务器将结果返回给请求的主机

![1553907569290](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553907569290.png)



# Linux下DNS服务器安装

## BIND简介：

BIND（Berkeley Internet Name Domain，伯克利因特网名称域）服务是全球范围内使用最广泛、最安全可靠且高效的域名解析服务程序。DNS域名解析服务作为互联网基础设施服务，其责任之重可想而知，因此建议大家在生产环境中安装部署bind服务程序时加上chroot（俗称牢笼机制）扩展包，以便有效地限制bind服务程序仅能对自身的配置文件进行操作，以确保整个服务器的安全。

## 安装bind服务和启动步骤：

```
yum install bind -y #安装dns服务
/etc/named.conf #主配置文件
systemctl start named.service #启动服务
systemctl enable named.service #开机启动服务
```

## DNS配置的主要文件组：

/etc/hosts　　主机的一个文件列表 　　添加记录如:111.13.100.92 www.baidu.com
对于简单的主机名解析（点分表示法），默认在请求DNS或NIS网络域名服务器前，/etc/named.conf 通常会告诉程序先查看此文件。
/etc/resolv.conf　　转换程序配置文件
在配置程序请求BIND域名查询服务查询主机名时，必须告诉程序使用哪个域名服务器和IP地址来完成这个任务
/etc/named.conf　　BIND主文件
设置一般的name参数，指向该服务器使用的域数据库的信息源
/var/named/named.ca　　根域名配置服务器指向文件
指向根域名配置服务器，用于告诉缓存服务器初始化
/var/named/localhost.zone　 localhost区正向域名解析文件
用于将本地IP地址（127.0.0.1）转换为本地回送IP地址（127.0.0.1）
/var/named/name.local　　localhost区反向域名解析文件
用于将localhost名字转换为本地回送IP地址（127.0.0.1）
/etc/named.rfc1912.zones　　区块设置文件
name.conf文件的主要配置信息：
acl　　定义ip地址的访问控制清单
control　　定义rndc使用的控制通道
include　　把其他的文件包含到配置文件中
key　　定义授权的安全密钥
logging　　定义日志内容和位置
options　　定义全局配置选项和默认值
server　　定义远程服务的特征
zone　　定义一个区

## DNS的资源记录（Resource Record, RR）格式：

DNS域名数据库有资源记录和区文件指令组成，由SOA（Start Of Authority起始授权机构记录，SOA 记录说明了在众多NS记录里那一台才是主名称服务器。
RR开始，同时包括NS RR；
正向解析文件包括A internet Address，作用，FQDN --> IP） 
MX （Mail eXchanger，邮件交换器）
CNAME（Canonical NAME 别名） 
反向解析文件包括PTR（PTR: PoinTeR，IP --> FQDN）
RR 语法：name　　[TTL]　　IN　　type　　value （字段之间由空格和制表符隔开）
注意： (1) TTL可从全局继承

​		(2) @可用于引用当前区域的名字 

​		(3) 同一个名字可以通过多条记录定义多个不同的值；此时 DNS服务器会以轮询方式响应 

​		(4) 同一个值也可能有多个不同的定义名字；通过多个不同的 名字指向同一个值进行定义；此仅表示通过多个不同的名字 可以找到同一个主机
SOA记录：name: 当前区域的名字，例如“heiye.com.” 　　

value: 有多部分组成 ：

​	(1) 当前区域的主DNS服务器的FQDN，也可以使用当前区域的名字；  

​	(2) 当前区域管理员的邮箱地址；地址中不能使用@符号，一般用.替换 如linuxedu.heiye.com

​	 (3) 主从服务区域传输相关定义以及否定的答案的统一的TTL 
例如： heiye.com.　　86400 　　IN 　　SOA 　　ns.heiye.com.	nsadmin.heiye.com. 　　(
​																					　　　 　　　　2015042201 ;
​																					　　　　　　　 序列号 2H ;
​																					　　　　　　　 刷新时间 10M ;
​																					　　　　　　　 重试时间 1W ;
​																					　　　　　　　 过期时间 1D ;
​																					　　　　　　　 否定答案的TTL值
​																				　　　			)
NS记录：name: 当前区域的名字 　　value: 当前区域的某DNS服务器的名字，例如 ns.heiye.com. 注意：一个区域可以有多个NS记录

例如：heiye.com. 　　IN 　　NS　　ns1.heiye.com.  

　　　heiye.com. 　　IN 　　NS 　   ns2.heiye.com.
注意：

 (1) 相邻的两个资源记录的name相同时，后续的可省略 

(2) 对NS记录而言，任何一个ns记录后面的服务器名字 ，都应该在后续有一个A记录
MX记录（Mail eXchanger）：name: 当前区域的名字 　　value: 当前区域的某邮件服务器(smtp服务器)的主机名 ， 一个区域内，MX记录可有多个；
但每个记录的value之前应 该有一个数字(0-99)，表示此服务器的优先级；数字越小优 先级越高  例如：
heiye.com. 　　IN 　　MX 　　10 　　mx1.heiye.com.

​           　　　　 IN 　　MX 　　20 　　mx2.heiye.com.
注意： (1) 对MX记录而言，任何一个MX记录后面的服务器名字 ，都应该在后续有一个A记录
A记录（Addrss）:name: 某主机的FQDN，例如www.heiye.com. 　　value: 主机名对应主机的IP地址
例如： www.heiye.com. 　　IN 　　A 　　1.1.1.1 　　
　　　 www.heiye.com.　　 IN 　　A 　　2.2.2.2 　　
　　　 mx1.heiye.com. 　　IN 　　A 　　3.3.3.3
　　　 mx2.heiye.com.    　 IN 　　A 　　4.4.4.4
　　　 *.heiye.com. 　　　  IN 　　A 　　5.5.5.5
　　　 heiye.com. 　　　　IN 　　A 　　 6.6.6.6 　　
避免用户写错名称时给错误答案，可通过泛域名解析进行解 析至某特定地址
其他记录：AAAA: name: FQDN 　　value: IPv6 　　　　
　　　　　PTR: name: IP，有特定格式，把IP地址反过来写，1.2.3.4，要写 作4.3.2.1；而有特定后缀：in-addr.arpa.，所以完整写法为 ：
4.3.2.1.in-addr.arpa. 　　value: FQDN
例如： 4.3.2.1.　　in-addr.arpa. 　　IN 　　PTR 　　www.heiye.com.
如1.2.3为网络地址，可简写成： 4 　　IN 　　PTR 　　www.heiye.com.
注意：网络地址及后缀可省略；主机地址依然需要反着写
别名记录:name: 别名的FQDN 　　value: 真正名字的FQDN
例如： www.heiye.com. 　　IN 　　CNAME 　　websrv.heiye.com.
named字段：
​	（1）根域以” . “结束，并且只有一个，没有上级域。而在Internet中，根域一般不需要表现出来。
​	（2）@：默认域，文件使用$ORIGIN domain 来说明默认域。
​	（3）ttl 全称”Time to Live “，以秒为单位记录该资源记录中存放高速缓存中的时间长度。通常此处设为空，表示采用SOA的最小ttl值。
​	（4）IN：将该记录标志为一个Internet DNS资源记录。
type字段:	(1)A记录：主机名对应的IP地址记录，用户可将该域名下网站服务器指向自己的Web服务器，同时也可设置域名的二级域名。
​	(2)MX记录：邮件交换记录可将该域下所有邮件服务器 指向自己的邮件服务器，只需在线填写服务器的IP地址。
​	(3)CNAME记录：别名记录，可允许多个名字映射到同一计算机，通常用于同时提供Web和邮件服务器的计算机。
​	(4)SOA记录：一个授权区的开始，配置文件的第一个记录必须是SOA的开始。
​	(5)PTR记录：用于地址到主机名的映射。
​	(6)HINFO记录：由一组描述主机的信息文件组成，通常包括硬件名称和操作系统名称。
value字段：
​	（1）A :存放IP地址。
​	（2）CNAME：设置主机别名。
​	（3）HINFO：通常为两行，分别对应Hareware（计算机硬件名称）和OS-type（操作系统名称）。
​	（4）NS：域名服务器的名称。
​	（5）PTR:主机真实名称。
测试检查配置文件错误的工具：nslookup、dig、named-checkzone、host、named-checkconf及dlint。

# Linux下DNS服务器配置实验

## 配置DNS正向解析：

主配置文件：定义区域

至少三个区域：

​	根、localhost、127.0.0.1

区域数据库文件：/var/named/

named：用户：named，组：named

主配置文件：

options {

​	//全局选项

}

zone "ZONE name" IN {

​	//定义区域

}



logging {

​	//定义日志系统

}

type {hint|master|slave|forward}

默认配置：

```
options {
	listen-on port 53 { 127.0.0.1; }; #监听IP地址
	listen-on-v6 port 53 { ::1; }; #IPv6的地址
	directory 	"/var/named"; #区域数据库存放位置
	dump-file 	"/var/named/data/cache_dump.db";#数据保留的位置
	statistics-file "/var/named/data/named_stats.txt";
	memstatistics-file "/var/named/data/named_mem_stats.txt";
	recursing-file  "/var/named/data/named.recursing";
	secroots-file   "/var/named/data/named.secroots";
	allow-query     { localhost; }; #允许本地主机进行查询
	recursion yes;#是否允许递归
	dnssec-enable yes; #允许dns安全设置
	dnssec-validation yes;
	bindkeys-file "/etc/named.iscdlv.key";
	managed-keys-directory "/var/named/dynamic";
	pid-file "/run/named/named.pid";
	session-keyfile "/run/named/session.key";
};
logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};
zone "." IN {
	type hint;
	file "named.ca";
};
include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
```

案例：asoneLinux.com

192.168.112.

mail：192.168.112.3

www：192.168.112.4

pop --> mail

ftp --> www

dns：192.168.112.2

/etc/named.conf配置文件内容

```
[root@localhost ~]# cat /etc/named.conf
options {
	directory "/var/named";
};

zone "." IN {
	type hint;
	file "named.ca";
};

zone "localhost." IN {
	type master;
	file "named.localhost";
};

zone "1.0.0.127.in-addr.arpa." IN {
	type master;
	file "named.loopback";
};

zone "asonelinux.com." IN {
	type master;
	file "asonelinux.com.zone";
};
```

/var/named/asonelinux.com.zone文件内容：

```
[root@localhost ~]# cat /var/named/asonelinux.com.zone 
$TTL 43200 
@	IN	SOA	dns.asonelinux.com. admin.asonelinux.com. (
								20190301
								2H
								10M
								7D
								1D )
@	IN	NS		dns
@	IN	MX	10	mail
dns	IN	A		192.168.112.2
mail	IN	A		192.168.112.3
www	IN	A		192.168.112.4
pop	IN	CNAME		mail
ftp	IN	CNAME		www
```

检查语法：

```
[root@localhost ~]# named-checkzone "asonelinux.com" /var/named/asonelinux.com.zone 
zone asonelinux.com/IN: loaded serial 20190301
OK
```

重新启动配置文件：

```
[root@localhost ~]# systemctl reload named.service
```

测试正向解析：

```
[root@localhost ~]# dig -t A www.asonelinux.com @192.168.112.2
; <<>> DiG 9.9.4-RedHat-9.9.4-73.el7_6 <<>> -t A www.asonelinux.com @192.168.112.2
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 59875
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;www.asonelinux.com.		IN	A

;; ANSWER SECTION:
www.asonelinux.com.	43200	IN	A	192.168.112.4

;; AUTHORITY SECTION:
asonelinux.com.		43200	IN	NS	dns.asonelinux.com.

;; ADDITIONAL SECTION:
dns.asonelinux.com.	43200	IN	A	192.168.112.2

;; Query time: 1 msec
;; SERVER: 192.168.112.2#53(192.168.112.2)
;; WHEN: Sat Mar 30 11:20:22 CST 2019
;; MSG SIZE  rcvd: 97
```

测试命令使用语法：

```
dig [-t type] [-x addr] [named] [@server]
host [-t type] {name} {server}
nslookup > 
	server DNS_SEVER_IP
	set q=TYPE
	{name}
```

编辑bind配置文件

```
[root@localhost ~]# cat /etc/named.conf
options {
	directory "/var/named";
};

zone "." IN {
	type hint;
	file "named.ca";
};

zone "localhost." IN {
	type master;
	file "named.localhost";
};

zone "1.0.0.127.in-addr.arpa." IN {
	type master;
	file "named.loopback";
};

zone "asonelinux.com." IN {
	type master;
	file "asonelinux.com.zone";
};

zone "192.168.112.in-addr.arpa." IN {
    type master;
    file "112.168.192.zone"
}
```

反向解析域配置数据库 

```
[root@localhost ~]# cat /var/named/112.168.192.zone 
$TTL 43200 
@	IN	SOA	dns.asonelinux.com. admin.asonelinux.com. (
								20190301
								2H
								10M
								7D
								1D );
	IN	NS	dns.asonelinux.com.
2	IN	PTR	dns.asonelinux.com.
3	IN	PTR	mail.asonelinux.com.
4	IN	PTR	www.asonelinux.com.
```

测试反向记录：

```
[root@localhost ~]# dig -x 192.168.112.3

; <<>> DiG 9.9.4-RedHat-9.9.4-73.el7_6 <<>> -x 192.168.112.3
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 9454
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;3.112.168.192.in-addr.arpa.	IN	PTR

;; ANSWER SECTION:
3.112.168.192.in-addr.arpa. 43200 IN	PTR	mail.asonelinux.com.

;; AUTHORITY SECTION:
112.168.192.in-addr.arpa. 43200	IN	NS	dns.asonelinux.com.

;; ADDITIONAL SECTION:
dns.asonelinux.com.	43200	IN	A	192.168.112.2

;; Query time: 0 msec
;; SERVER: 192.168.112.2#53(192.168.112.2)
;; WHEN: Sat Mar 30 14:48:32 CST 2019
;; MSG SIZE  rcvd: 122
```

BIND子域授权管理

在父域的配置文件中添加如下选项：

​	授权的子区域名称

​	子区域的名称服务器

​	子区域的名称服务器的IP地址

访问控制列表：

acl ACL_NAME {

​	IP；

}

编译安装

```
yum install -y openssl openssl-devel libcap-devel libidn2-devel nettle-devel 
cd /usr/local/src/
wget -o bind-9.14.0.tar.gz https://www.isc.org/downloads/file/bind-9-14-0/?version=tar-gz
tar xvf bind-9.14.0.tar.gz
cd bind-9.14.0
./configure
make
make install
```

