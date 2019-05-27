Linux网络基础
	计算机网络：
		TCP/IP协议族（使用）
			ISO，OSI：协议栈（学习）
		MAC：media access control
			48bits；
			ICANN：24bits，2^24
				地址块：2^24
			网桥：MAC地址表
				静态指定：
				动态学习：根据原地址学习；
		IP（Internet Protocol）地址=网络号+主机号
			A<-->B
				网路？
				主机？
			IPv4：32bits
				8bits.8bits.8bits.8bits
					0-255
					0.0.0.0-255.255.255.255
				IP地址分类：
					A类：
						第一段为网络号，后三段为主机号
						网络号：
							0 000 0000——0 111 1111：1——127
						网络数126，127
						每个网络的主机数：2^24-2
						默认子网掩码：255.0.0.0，/8
							用户于IP地址按位进行“与”运算，从而取出其网络地址；
							1.3.2.1/255.0.0.0=1.0.0.0
							1.3.2.1/255.255.0.0=1.3.0.0
						私网地址：10.0.0.0/255.0.0.0
					B类：
						前两段为网络号，后两段为主机号
						网络号：
							10 00 0000——10 11 1111：128——191
							网络数：2^14
							每个网络中的主机数量：2^16-2
							默认子网掩码：255.255.0.0，/16
							私网地址：172.16.0.0——172.31.0.0
					C类：
						前三段为网络号，最后一段为主机号
						网络号：
							110 0 0000——110 1 1111：192-223
							网络数：2^21
							每个网络中的主机数：2^8-2
							默认子网掩码：255.255.255.0，/24
					D类：组播
						1110 0000——1110 1111:224-239
					E类：科研
						240-255
			IPv6：128bits
			路由器：router
				路由表：
					静态指定
					动态学习：rip2，ospf
				路由条目：
					目标地址 下一跳（nexthop）
						目标地址的类别：
							主机：主机路由
							网络：网络地址
							0.0.0.0/0.0.0.0：默认路由
	OS：多用户，多任务
		多任务，多线程
			Chrome
		通信时，进程的数字标识：
			16bits：
				0——65535:1——65535
					1——1023：固定分配，而是只有管理员有权限启用；
					1024——4w：半固定，
					4w+：临时；
		进程地址：
			IP：Port，Socket
	总结：
		MAC：本地通信；范围：本地局域网
		IP：界定通信主机，源和目标；范围：互联网
		Port：界定进程，范围：主机；
	将linux主机接入到网络中：
		IP/Netmask：本地通信
		路由（网关）：跨网络通信
		DNS服务器地址：基于主机名的通信
			主DNS服务器地址
			备用DNS服务器地址
			第三个DNS服务器地址
		配置方式：
			（1）静态指定
				命令：
					ifcfg家族：
						ifconfig：配置IP，Netmask
						route：路由
						netstat：状态及统计数据查看工具
					iproute2家族：
						ip OBJECT
							addr：地址和掩码
							link：接口
							route：路由
						ss：状态及统计数据查看
					centos7：nm（network Manager）家族
						nmcli：命令行工具
						nmtui：text windows 工具
					注意：
						（1）DNS服务器指定：
							配置文件：/etc/resolv.conf
						（2）本地主机名配置
							hostname
							配置文件：/etc/sysconfig/network
							centos7：hostnamectl
				配置文件：
					RedHat及相关发行版
						/etc/sysconfig/network-scripts/ifcfg-NETCARD_NAME
			（2）动态分配：依赖于本地网络中有DHCP服务
				DHCP：Dynamic Host Configure Protocol
	网络接口命名方式：
		传统命名：
			以太网：ethX，[0,oo]，例如eth0，eth1，….
			PPP网络：pppX，[0，….]，例如ppp0，ppp1，….
		可预测命令方案（CentOS）
			可支持多种不同的命名机制：
				Fireware，拓扑结构
			（1）如果Fileware或BIOS为主板上集成的设备提供的索引信息可用，则根据此索引进行命名，如eth0，eth1
			（2）如果fireware或BIOS为PCI-E扩展槽所提供的索引信息可用，且可预测，在根据此索引进行命名，如ens1，ens2
			（3）如果硬件接口的物理为信息可用，则根据此信息命名，如enp2s0
			（4）如果用户显示定义，可也根据MAC地址命名，例如enx122161ab2e10
			上述均不可用，则仍然使用传统方式命名；
			命名格式的组成：
				en：Ethernet
				wl：wlan
				ww：wwan
				名称类型：
					o<index>：集成设备的设备索引号
					s<slot>：扩展槽的索引号；
					x<MAC>：基于MAC地址的命名
					p<bus>s<slot>：基于总线及槽的拓扑结构进行命名；
	网络管理工具：
		ifcfg命令家族：ifconfig，route，netstat
			ifconfig命令：接口及地址查看和管理
				ifconfig [-v] [-a] [-s] [interface]
				ifconfig [-v] interface [aftype] options | address …
					ifconfig -a：显示所有接口，包括inactive的状态接口；
					ifconfig iface ip/netmask [up|down]
					ifconfig iface netmask 
					选项：
						[-]promisc
					注意：命令配置送往内核的TCP/IP协议栈，并生效；
				管理IPv6地址：
					add addr/prefixlen
					del addr/prefixlen
			route命令：路由查看及管理
				路由条目类型：
					主机路由：目标地址为单个IP；
					网络路由：目标地址为IP网络；
					默认路由：目标任意主机，0.0.0.0/0.0.0.0
				查看：
					route -n
				添加：
					route  [-v]  [-A family |-4|-6] add [-net|-host] target [netmask Nm] [gw Gw] [metric N]  [mss M] [window W] [irtt I] [reject] [mod] [dyn] [reinstate] [[dev] If]
					示例：route add -net 10.0.0.0/8 gw 172.20.0.1
						route add -net 0.0.0.0/0.0.0.0 gw 192.168.10.1
						route add default gw 192.168.10.1
				删除：
					route  [-v] [-A family |-4|-6] del [-net|-host] target [gw Gw] [netmask Nm] [metric  N] [[dev] If]
					示例：route del -net 10.0.0.0/8
						route del default
			netstat命令：
				Print network connections, routing tables, interface statistics, masquerade connections, and multicast memberships
				显示路由信息：netstat -rn
					-r：显示内核路由表
					-n：数字标识
				显示网络连接：
					netstat  [address_family_options]  [--tcp|-t]  [--udp|-u] [--udplite|-U] [--sctp|-S] [--raw|-w] [--listening|-l] [--all|-a] [--numeric|-n] [--numeric-hosts] [--numeric-ports] [--numeric-users] [--symbolic|-N] [--extend|-e[--extend|-e]] [--timers|-o] [--program|-p] [--verbose|-v] [--continuous|-c] [--wide|-W] [delay]
						-t：tcp协议相关的连接，均有其状态；FSM（Finate State Machine）；
						-u：udp相关的连接
						-w：raw socket相关的连接
						-l：处于监听状态的连接
						-a：所有状态
						-n：以数字格式显示ip和port
						-e：扩展格式
						-p：显示相关的进程及PID；
					常用组合：
						-tan，-uan，tnl，-unl，-tunlp
					传输层协议：
						tcp：面向连接的协议；通信开始前，要建立一个虚电路；
						udp：无连接的协议；直接发送数据报文；
				显示接口相关统计数据：
					netstat  {--interfaces|-I|-i}  [--all|-a]  [--extend|-e]  [--verbose|-v] [--program|-p] [--numeric|-n] [--numeric-hosts] [--numeric-ports] [--numeric-users] [--continuous|-c] [delay]
					所有接口：
						netstat -I
					指定接口：
						netstat -I<IFACE>
			ifup/ifdown命令：
				注意：通过配置文件/etc/sysconfig/network-scripts/ifcfg-IFACE_NAME来实现接口并完成配置；
			配置主机名：
				hostname命令：
					查看：hostname
					配置：hostname HOSTNAME
						当前系统有效，重启后无效
				hostnamectl命令：
					hostnamectl status ：显示当前主机名信息；
					hostnamectl set-hostname 设定主机名，永久有效；
				配置文件：/etc/sysconfig/network
					HOSTNAME=<HOSTNEM>
					注意：吃方法的设置不会立即生效，需要重启；
			配置DNS服务器地址指向：
				配置文件：/etc/resolv.conf
					nameserver DNS_SERVER_IP
					如何测试（host/nslookup/dig）：
						dig -t A FQDN
							FQDN-->IP
						dig -x IP
							IP--> FQDN
		iproute2家族：
			ip命令：
				show / manipulate routing, devices, policy routing and tunnels
				ip [ OPTIONS ] OBJECT { COMMAND | help }
					OBJECT := { link | address | addrlabel | route | rule | neigh | ntable | tunnel | tuntap | maddress | mroute | mrule | monitor | xfrm | netns | l2tp | tcp_metrics | token | macsec }
				注意：OBJECT可以简写，各OBJECT也可以简写
				ip OBJECT：
				ip link：network device configuration
					ip link set：change device attributes
						ip link set DEV_NAME（default）：指明要管理的设备，dev关键字可省略
							up and down：
							multicast off or multicast on：启用或警用组播或多播功能
							name NAME重命名
							mtu NMUBER：设置MTU的大小，默认为1500；
							netns PID：ns为namespace，用于将接口移动到指定的网络名称空间；
							help：显示简要使用帮助
					ip link show ：display device attributes
				ip netes：IP-manage network namespace
					list：列出所有的netns
					add NAME：创建指定的netns
					del NAME：珊瑚指定的netns
					exec NAME COMMAND：在指定的netns中运行命令
				ip addr-manage protocol address
					ip addr add new protocol address
						ip addr add IFADDR dev IFACE
						lable NAME：为额外的地址添加一个别名
						broadcast ADDRESS：广播地址，会根据IP和NETMASK自动计算得到；
						scope global
							global：全局可用
							host：接口可用
							link：仅本机可用
					ip addr show - show protocol address
						ip addr list [IFACE] ：仅显示指定接口的地址；
					ip addr delete - delete protocol address
						ip addr delete IFACE dev IFACE
					ip addr flush - flush protocol address
						ip addr flush dev IFACE
				ip route：routing table entry.
					ip route add ： add new network route
						ip route add TYPE PREFIX via[GW] [dev] [src SOURCE_IP]
						示例：
							ip route add 192.168.0.0/24 via 10.0.0.1 dev eth1 src 10.0.20.100
							ip route add default via GW
					ip route change：change existing network route
					ip route replace：add or change network route
					ip route delete：delete network route
						ip route delete TYPE PREFIX
						示例：
							ip route delete 192.168.1.0/24
					ip route show：list network routes
						TYPE PREFIX 
					ip route get-get a single network route
						ip route get TYPE PREFIX
					ip route flush-flush network routes
						TYPE PREFIX 
			ss命令：
				ss [options] [ FILTER ]：
					选项：
						-t：tcp协议的相关连接
						-u：udp相关连接
						-w：raw socket相关的连接
						-l：监听状态的连接
						-a：所有状态的连接
						-n：数字格式
						-p：相关的程序及其PID
						-e：扩展格式信息
						-m：内存用量
						-o：计时器信息
					过滤器选项：
						FILTER := [ state STATE-FILTER ] [ EXPRESSION ]
						TCP常见状态：
							TCP FSM：
								LISTEN：监听
								ESTABLISEHD：建立的连接
								FIN_WAIT_1：
								FIN_WAIT_2：
								SYN_SENT：
								SYN_RECV：
								CLOSED：
							EXPRESSION：
								dport=
								sport=
									示例：'(dport=:22 or sport=:22)'
										ss -tan '( dport = :22 or sport = :22 )'
	配置文件：
		IP/NETMASK/GW/DNS等属性的配置文件：/etc/sysconfig/network-scripts/ifcfg-IFACE
			IFACE：接口名称；
		路由的相关配置文件：/etc/sysconfig/network-scripts-IFACE
		配置文件/etc/sysconfig/network-scripts/ifcfg-IFACE通过大量参数来定义接口及其属性；通过vim等文本编辑器直接修改，可以刻使用专用的命令进行修改（CentOS6：system-config-network(setup)，CentOS7：nmtui）
		ifcfg-IFACE配置文件参数
			DEVICE：此配置文件对应的设备名称；
			ONBOOT：在系统引导过程中，是否激活此接口
			UUID：此设备是否惟一标识
			IPV6INIT：是否初始化IPV6
			BOOOTPROTO：激活此接口时使用什么协议来配置接口属性，常用的有dhcp，static，none；
			TYPE：接口类型，常见的有Ethernet，Bridge
			DNS1：第一DNS服务器指向
			DNS2：备用DNS服务器指向
			DOMANT：DNS搜索域
			IPADDR=IP地址
			NETMASJ：子网掩码，Centos7以长度指定子网掩码（PREFIX）
			GATEWAY：默认网关
			USERCTL：是否允许普通用户控制此设备
			HWADDR：设备的MAC地址；
			PERDNS：是否允许BOOTPROTO的值“dhcp”，是否允许dhcp server分配dns服务器指向覆盖本地手动指定的DNS服务器指向；默认为允许；
			NM_CONTROLLED：是否使用NetworkManager服务来控制接口；
		网络服务：
			network
			NetworkManager
			管理网络服务：
				CentOS6：service SERVICE {start|stop|restart|status}
				CentOS7：systemctl {start|stop|restart|status} SERVICE.service
			配置文件修改之后，如果要生效，需要重新启动服务
				service network restart
	用到非默认网关路由：/etc/sysconfig/network-scripts/route-IFACE
		支持两种配置方式，但是不可混用；
			（1）每行一个路由条目
				TARGET via GW
			（2）每三行一个路由条目：
				ADDSS#=TARGET
				NETMASK#=MASK
				GATEWAY#=NEXTHOP
	给接口配置多个地址：
		ip addr之外，ifconfig或配置文件都可以；
		（1）ifconfig IFACE_LABLE IPADDR/NETMASK
			IFACE_LABLE：eth0:0，eth0:1，….
		（2）为别名添加配置文件；
			DEVIE=IFACE_LABLE
			BOOTPROTO：网上别名不支持获取动态地址
	nmcli命令：
		nmcli [OPTIONS...] {help | general | networking | radio | connection | device | agent | monitor} [COMMAND] [ARGUMENTS...]
			nmcli device {status | show | set | connect | reapply | modify | disconnect | delete | monitor | wifi | lldp} [ARGUMENTS...]
			nmcli connection {show | up | down | modify | add | edit | clone | delete | monitor | reload | load | import | export} [ARGUMENTS...]
				modify [--temporary] [id | uuid | path] ID {option value | [+|-]setting.property value}…
		如何修改IP地址等属性：
			nmcli conn modify IFACE [+]-setting.property value
				ipv4.address
				ipv4.gateway
				ipv4.dns1
				ipv4.method
					manual
	网络客户端工具：
		ping/lftp/ftp/wget
		ping命令：
			- send ICMP ECHO_REQUEST to network hosts
			ICMP：Internet control message Protocol
			ping [OPTION] destination
				-c：指定发送icmp包的个数
				-w #：ping命令超时时长
				-W #：一次ping操作中，等待对方相应的超时时长；
				-s #：指明ping包报文大小
		hping命令：（package：hping3）
			- send (almost) arbitrary TCP/IP packets to network hosts
			--fast
			--faster
			-flood
			-l uX
		traceroute命令：
			- print the route packets trace to network host
			跟踪从源主机到目标主机之间经过的网关；
		ftp命令：
			— Internet file transfer program
			ftp服务命令行客户端工具：
		lftp命令：
			lftp [-d] [-e cmd] [-p port] [-u user[,pass]] [site]
			get，mget
			put，mput
			rm，mrm
		lftpget命令：
			- get a file with lftp(1)
			lftpget [-c] [-d] [-v] URL [URL...]
				-c：继续此前的下载
		wget命令：
			The non-interactive network downloader.
			wget [option]... [URL]…
				-b：在后台执行下载操作；
				-q：静默模式，不显示下载进度；
				-O file：保存的下载的文件位置；
				--limit-rate=ampunt：以指定的速率传输文件；
				-c：断点续传；
博客作业：ifconfig，ip/ss配置文件，nmap，ncat
