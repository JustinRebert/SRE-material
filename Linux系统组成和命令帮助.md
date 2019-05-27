回顾：
	Debian，Slackware，RedHat
		Debian：
			Ubuntu
			Knopix
		Slackware：
			S.u.S.E
				OpenSUSE
		RedHat：
			CentOS
			Fedora
		Linux：内核
			Application
			GNU/Linux：通常以源码（文本格式方式提供）：
				自由、灵活：
Linux基础：
	CPU架构
		x86
		x64（amd64）
		m68000，m68k
		arm
		ultraspac solaris
		power，aix
		powerpc，ppc
		MIPS
		alpha hp-ux
	POS：POSIX
		API：Application Program Interface
	ABI：Application Binary Interface
	Linux，BSD，AIX：Unix-like
		Unix类的系统：
			API
			ABI
	Stallman：GNU
		闭源：
		开源：提供源代码：
			开源协定：
				GPL：General Public License
					Copyright，copyleft
					LGPL：Lesser GPL
					GPLv2，GPLv3
					FSF：free SF
				BSD：
				APache：
					ASF：Apache Software Foundation
			双线授权：
				Community：遵循开源协定：
				Enterprise：
	程序管理：
		程序的组成部分：
			二进制程序：
			配置文件：
			库文件：
			帮助文件：
		X,Y,Z
		程序包管理器：
			X：
				程序的组成文件打包成一个或者有限几个文件；
				安装：
				卸载：
				查询：
		Debian：deb，apt-get
		RedHat：rpm，yum，dnf
		S.u.S.E：rpm，zypper
		ArchLinux：port
		Gentoo：
		LFS：Linux From Scratch
		Kali：
	安装Linux：
		虚拟机：Linux
			计算机：CPU，内存，IO
				Vmware workstation
				virtualbox
	CentOS的镜像：
		http://mirrors.aliyun.com
		http://mirrors.163.com
		http://mirrors.sohu.com
	教室安装：
		Cobbler：
			硬盘大小：120G
			网卡：桥接
		管理员：root/magedu
	ISO镜像：
		硬盘分区：流出一些空间，实在不能，可安装完成以后，新增一块虚拟硬盘；
	终端：terminal
		用户界面：
			GUI：
				Gnome
				KDE
			CLI：
			bash，zsh，sh，csh，tcsh，ksh
				查看所用的shell的类型：
					~]# echo $SHELL
		远程连接：
			ssh协议:secure shell：
			~]# ss -tnl
				查看系统是否处于监听tcp协议的22端口
			~]# ip addr list
			~]# ifconfig
				查看IP地址：
			确保防火墙处于关闭状态：
				~]# iptables -L -n
				CentOS 7：
					~]# systemctl disable firewalld
					~]# systemctl stop firewalld
				CentOS 6：
					~]# service iptables stop
					~]# chkconfig iptables off
	终端设备：
		多任务、多用户
		物理终端，控制台：console
		虚拟终端：6个：tty
			Ctrl+Alt+F[1-6]
		图形中孤单：
			CentOS6：
				Ctrl+Alt+F7
			CentOS7：
				在哪个虚拟终端启动，即位于哪个虚拟终端：
		穿行终端：ttyS
		伪终端：pty
		Linux的哲学思想之一：
			一切皆文件
				物理终端：/dev/console
				虚拟终端：/dev/tty#[1,6]
				穿行终端：/dev/ttyS#
				伪终端：/dev/pts/#
		注意：在启动设备之后，在其上关联一个用户接口程序，即可实现与用户交互：
			查看终端设备：
				~]# tty
			查看接口程序：
				~]# echo $SHELL
			启动GUI：
				在某一虚拟终端接口运行命令 startx &
	CLI接口：
		命令行接口：
			[root@centos7 ~]# command
				prompt：
					root：当前登录的用户
					centos7：当前主机的主机名，非完整格式，在此完整格式诶asone.centos7.ink
					~：用户当前所在目录（Current directory），也称为工作目录（working directory）：相对路径
					#：命令提示符：
						#：管理员账户，为root；拥有最高权限，能执行所有操作；
						$：普通用户，非root用户：不具有管理权限，不能执行系统管理类操作
					注意：建议使用非管理操作时切换至管理员，操作完成时即退回去：
	几个基础命令：
		tty：查看当前的终端设备：
		ifconfig或ip addr list：查看活动接口的ip地址：
		echo：回显
		ping：探测网络的目标主机与当前主机之间的连通性
			ctrl+C：终止命令执行
		关机命令：
			centos7：
				~]# systemctl poweroff
				~]# systemctl reboot
			*：
				~]# poweroff
				~]# halt
				~]# reboot
	Stallman：
		自由含义：自由学习和修改；自由使用；自由分发；自由创建衍生版；
	Linux的哲学思想：
		1、一切皆文件：
			把几乎所有资源统统抽象为文件形式：包括硬件设备，甚至通信接口等；
				open（），read（），write（），close（），delete（），create（）
		2、由众多功能单一的程序组成：一个程序只做一件事，并且做好；
			组合小程序完成复杂任务；
		3、尽量避免跟用户交互：
			目标：易于以编程的方式实现自动化任务；
		4、使用文本文件保存配置信息；
	思考：文件是什么？众多文件如何有效组织起来？
		目录：路径映射；
		文件：存储空间存储的一段流式数据，对数据可以做到按名存取；
		文件系统：层次结构：有索引；
			/：原始起点：
			倒置树状结构：
			/dev/pts/2：
				最左侧/：表示根目录
				其他的/：表示路径分隔符
					Linux的路径分隔符是/
					Windows的是\
			文件的路径表示：
				绝对路径：从根开始表示出的路径
				相对路径：从当前位置开始表示出的路径
			文件名使用法则：
				严格区分字符大小写：file1，File1，FILE1
				目录也是文件，在统一路径下，两个文件不能同名；
				支持使用除/以外的任意字符；
				最长不能超过255个字符；
			用户有家目录：home，
				用户的起始目录：普通用户管理文件的位置：
			工作目录：
				/etc/sysconfig/network-scripts/ifcfg-en01677736
					basename：最左侧的文件或目录名：
					dirname：base的左侧路径：
				命令：
					~]# basename /PATH/TO/SOMEFILE
						SOMEFILE
					~]# dirname /PATH/TO/SOMEFILE
						/PATH/TO
回顾：
	开源协定：
		GPL、LGPL、Apache
	BSD：
		1、如果二次发布的产品中包含源代码，则在源代码中必须带有原来的代码中的BSD协定；
		2、如果二次发布的产品是二进制格式的库文件或程序，则需要在发布的文档或版权声明包含原来的代码中的BSD协定；
		3、不可以用开源代码的作者或者组织，以及原来的产品的名字做市场推广；
	代码的共产主义运动：
	文件系统、文件、目录：
		倒置树状结构：
			目录：
			文件：
		文件名：严格区分字符大小写：路径分隔符/：
	路径：
		绝对路径：
		相对路径：
	程序的组成部分：二进制程序、库文件、配置文件、帮助文件；
			二进制、库文件：可执行文件
		配置文件、帮助文件：可被查看内容的文件
		《穿越计算机的迷雾》、《箱子物理史话》
	人机交互界面：
		GUI：
		CLI：
			[login@hostname workdir]# COMMAND
Linux基础知识：
	命令的语法通用格式：
		~]# COMMAND OPTIONS ARGUMENTS
		COMMAND
			发起一个命令：请求内核将某个二进制程序运行为一个进程：
				程序--> 进程
				静态--> 动态（有生命周期）
			命令本身是一个可执行的程序文件：二进制格式的文件，有可能被调用共享库文件；
				多数程序文件都存放在：/bin，/sbin，/usr/bin，/usr/local/bin，/usr/local/sbin
					普通命令：/bin，/usr/bin，/usr/local/bin
					管理命令：/sbin，/usr/sbin，/usr/local/sbin
				共享库：/lib，/lib64，/usr/lib，/usr/lib64，/usr/local/lib，/usr/local/lib64
					32bits的库：/lib，/usr/lib，/usr/local/lib
					64bits的库：/lib64，/usr/lib64，/usr/local/lib64
				注意：并非所有的命令都有一个在某个目录与之对应的可执行程序文件
			命令必须遵循特定格式规范：exe，msi，elf（linux）
				~]# file /bin/ls
			命令分类：
				有shell程序自带的命令：内置命令（builtin）
				独立的可执行程序文件，文件名即命令名：外部命令
			shell程序是独特的程序，负责解析用户提供的命令；
				环境变量：
					PATH：从哪些路径中查找用户键入的命令字符串所对应的命令文件
					~]# echo $PATH
					/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
					查找次序：自左而右；
			查看命令类型：
				type COMMAND
		OPTIONS：
			指定命令的运行特性：
			选项有两种表现形式：
				短选项：-C，例如-l，-d
					注意：有些买哪个行没有-；
					如果同意命令同时使用多个短选项，多数可以被合并：-l -d=-ld
				长选项：--word，例如--help，--human-readable
				注意：有些选项可以带参数，此称为选项参数
		ARGUMENTS：
			命令的作用对象：命令对什么生效；
			注意：不同的参数的命令的参数：有些命令可同时带多个参数，多个之间以空白字符分隔；
		例如：ls -ld /var /etc
	获取命令的帮助：
		内部命令：
			help COMMAND
			外部命令：
				（1）命令自带简要格式的使用帮助
					~]# COMMAND --help
				（2）使用手册：manual
					/usr/share/man
					~] # man COMMAND
						SECTION：
							NAME：功能性说明
							SYNOPSTS： 语法格式
							Description：描述
							OPTIONS：选项
							Example：使用示例
							Author：作者
							BUGS：报告程序bug的方式
							SEE ALSO：参考
							….
						SYNOPSIS：
							[]：可选内容
							<>:必须提供的内容：
							a|b|c:多选一：
							….：同类内容可出现多个：
					使用手册：压缩格式的文件，有章节之分
					/usr/share/man
						man1，man2，…….
						1、用户命令；
						2、系统调用
						3、C库调用
						4、设备文件及特殊文件
						5、文件格式（配置文件格式）
						6、游戏使用帮助
						7、杂项
						8、管理工具及守护进程
					~] # man CHAPTER COMMAND
						注意：并非每个COMMAND在所有章节下都有手册
							查看：
								~] # whatis COMMAND
								注意：其执行过程是查询数据库进行的
									手动更新数据库：
										~] # makewhatis
				man命令打开手册以后的操作方法：
					翻屏
						空格键：向文件尾部翻一屏
						b：向文件首部翻一屏
						ctrl+d：向文件尾部翻半屏
						ctrl+u：向文件首部翻半屏
						回车键：向文件尾部翻一行
						k：向文件首部翻一行
						G：跳转至最后一行
						#G：跳转至指定行
						1G：跳转至文件首部
					文本搜索：
					/keyword：从文件首部向文件尾部依次查找
					？keyword：从文件尾部向文件首部依次查找
						n：与查找命令方向相同
						N：与查找命令方向相反
					退出：
						q：quit
				选项：
					-M /PATH/TO/SOMEDIR：到指定目录下查找命令手册并打开
				练习：获取useradd命令用法
					（1）添加用户gentoo
						~] # useradd gentoo
						~] # id gentoo
					（2）添加用户Slackware，要求指定其所用的shell为/bin/tcsh
						~] # useradd -s /bin/tcsh Slackware
						~] # tail -l /etc/passwd
			（3）info COMMAND
				获取命令的在线文档
			（4）很多应用程序会自带帮助文档：/usr/share/doc/APP-VERSION
				README：程序的相关的信息：
				INSTALL：安装帮助；
				CHANGES：版本迭代时的改动信息；
			（5）主流发行版官方文档
				http://www.redhat.com/doc
			（6）程序官方的文档：
				官网站点上的“Document”
			（7）搜获引擎
				google
					keyword filetype:pdf
					keyword site:domain.tld
					….
		书籍的出版社：
			O'Reiley
			Wrox
			机械工业、电子工业、人邮、清华大学、水利水电