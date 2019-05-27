回顾：
	如何获取使用帮助：
		help，man，info
		自带文档（README，CHANGELOG，INSTALL），官方文档
		发行版的文档
		Google
		Linux kernel：Documention
		manual：使用手册
			 whatis keyword
			man # keyword
				SECTION：
					NAME
					SYNOPTIONS
						[]，<>，|，{}，….
					….
				控制命令：
					Space，b
					Enter，K
					Ctrl+d，Ctrl+u
					q
					#G
					/keyword
					？keyword
						n
						N
	常用基础命令：pwd，cd，ls，echo，data，clock，cal，hwclock，cat，tac，file，type，tty，halt，poweroff，shutdown，reboot
Linux文件系统：
	Linux：glibc
		程序编译方式：
			动态链接
			静态编译
	进程的类型：
		终端：硬件设备，关联一个用户接口
		与终端相关：通过终端启动
		与终端无关：操作引导启动过程当中自动启动
	操作系统的组成：
		静态：kernel，application
		文件系统：层级结构 
	FHS：Filesystem Hierarchy Standard
		/bin：所有用户可用的基本命令程序文件；
		/sbin：所有系统管理可用的基本命令程序文件；
		/boot：引导加载器用到的各种静态文件：kernel，initramfs（initrd），grub；
		/dev：存储特殊文件或设备文件；
			设备有两种类型：字符设备（线性设备）；块设备（随机设备）；
		/etc：系统程序的配置文件，只能为静态的；
		/home：普通用户家目录的几种位置；一般每个普通用户默认为此目录下与用户名同名的家目录：/home/USERNAME；
		/root：管理员的家目录；可选；
		/lib：为系统启动或根文件系统上的应用程序（/bin，/sbin等）提供共享库文件，以及为内核提供内核模块；
			lib.so.*：动态链接的C库；
			ld.*：运行时连接器/加载器；
			modules：用于存储内核模块的目录；
		/lib64：64位系统；
		/media：便携式设备挂载点，cdrom，floppy等；
		/mnt：其他文件系统的临时挂载点；
		/opt：附加应用程序的安装位置，可选路径；
		/srv：当前主机为服务提供的数据；
		tmp：为会产生临时文件的程序提供的用于存储临时文件的目录，可供所有用户执行写入操作； 有特殊权限；
		/usr：usr Hierarchy，全局共享的只读数据路径；
			bin，sbin
			lib，lib64
			include：C程序头文件
			share：命令手册也和自带文档等架构特有的文件的存储位置
			local：另一个层级目录
			X11R6：X-Window程序的安装位置
			src：程序源文件的存储位置
		/usr/local：Local hierarchy，让系统管理员安装本地应用程序；通常用于安装第三方程序
		var：/var Hierarchy ，存储常发生变化的数据的目录
			cache 	Application cache data
			lib 	Variable state information
			local 	Variable data for /usr/local
			lock 	Lock files
			log 	Log files and directories
			opt 	Variable data for /opt
			run 	Data relevant to running processes
			spool 	Application spool data
			tmp 	Temporary files preserved between system reboots
		/proc：基于内存的虚拟文件系统，用于为内核及进程存储其相关信息：它们为内核参数，例如net.ipv4.ip_forward，可虚拟为net/ipv4/ip_forward，存储于/proc/sys/，因此其完整路径为/proc/sys/net/ipv4/ip_forward；
		/sys：sysfs虚拟文件系统提供了一种比proc更为理想的访问内核数据的途径；其主要作用为管理员提供统一模型的接口
			参考： https://www.ibm.com/developerworks/cn/linux/l-cn-sysfs/
	《奇点临近》
