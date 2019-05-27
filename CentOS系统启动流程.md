CentOS系统启动流程
	Linux系统的组成部分：内核+根文件系统
		内核：进程管理、内存管理、网络协议栈、文件系统、驱动程序、安全功能
		IPC：Inter Process Communication
			消息队列、Semerphor、shm
			Socket
	运行中的系统环境可以分为两层：内核空间、用户空间
		用户空间：应用程序（进程或线程）
		内核空间：内核代码（系统调用）
	内核设计流派：
		单内核设计：把所有功能集成与同一个程序；
			Linux
		微内核设计：每种功能使用一个单独的子系统实现；
			Windows，Solaris
		Linux内核特点：
			支持模块化：.ko（kernel object）
			支持模块运行时动态装载或卸载；
			组成部分：
				核心文件：/boot/vmlinuz-VERSION-release
				randisk：
					CentOS5：/boot/initrd-VERSION-release.img
					CentOS6，7：/boot/initramfs-VERSION-release.img
				模块文件：/lib/modules/VERSION-release
	CentOS 系统的启动流程：
		POST：加电自检；
			ROM：CMOS
				BIOS：Basic Input and Output System
			ROM+RAM
		Boot Sequence：
			按次序查找引导设备，第一个有引导的设备即为本次启动要用到的设备；
			bootloader：引导加载器，程序；
				Windows；ntloader
				Linux：
					LILO：Linux LOader
					GRUB：Grand Uniform Bootloader
						GRUB 0.X：Grub Legacy
						GRUB 1.X：Grub2
				功能：提供一个菜单，运行用于选择要启动的操作系统或不同的内核版本；把用户选定的内核装载到RAM中的特定空间中，解压、展开，而后把系统控制权移交给内核；
				MBR：Master Boot Record
					512bytes：
						446bytes：BootLoader
						44bytes：fat
						2bytes：55AA
				GRUB：
					BootLoader：1st stage
					Partition：/boot/grub，2nd，stage
				注意：UEFI，GPT
			kernel：
				自身初始化：
					探测可识别到的硬件设备；
					加载硬件驱动程序；（有可能会借助于ramdisk加载驱动）
					以只读方式挂载根文件系统；
					运行用户空间的第一个应用程序：/sbin/init
				Init程序的类型：
					CentoS5：sysV init
						配置文件：/etc/inittab
					CentOS6：Upstart
						配置文件：/etc/inittab
							/etc/init/*.conf
					CentOS7：Systemd
						配置文件：/usr/lib/systemd/system/，/etc/systemd/system/
				ramdisk：
					Linux内核的特性之一：使用缓冲和缓存来加速对磁盘上的文件访问；
						ramdisk-->ramfs
						centos5：initrd
							工具程序：mkinitrd
						Centos6,7：initramfs
							工具程序：dracut，mkinitrd
			系统初始化流程（内核级别）：POST--> BootSequence（BIOS）--> BootLoader（MBR）--> Kernel（ramdisk） --> rootfs（readonly） --> /sbin/init()
		/sbin/init：
			CentOS5：SysV init
				运行级别：为了系统的运行或维护等目的而设定的机制；
					0-6:7个级别：
						0：关机：shutdown
						1：单用户模式（Sigle user），root用户，无需认证；维护模式；
						2：多用户模式（multi user），会启动网络功能，但不会启动NFS，维护模式；
						3：多用户模式（multi user），完全功能模式，文本界面；
						4：预留级别，目前无特别适用模式目的，但习惯以同3级别功能使用；
						5：多用户模式（multi user），完全功能模式，图形界面；
						6：重启，reboot
					默认级别：3,5
					级别切换：init #
					级别查看：
						who -r
						 runlevel
				配置文件：/etc/inittab
					每行定义一种action以及与之对应的process
						id：runlevel：action：process
							id：一个任务的标识符；
							runlevels：在哪些级别启动此任务；#，###，也可以为空，表示所有级别；
							action：在什么条件下启动此任务；
							proces：任务；
						action：
							wait：等待切换至任务所在的级别时执行一次；
							respawn：一旦此任务终止，就自动重新启动它；
							initdefault：设定默认运行级别；此时，process省略
							sysinit：设定系统初始化方式，此处一般为指定/etc/rc.d/rc.sysinit脚本；
						例如：
							id：3：initdefault
							si：：sysinit：/etc/rc.d/rc.sysintit
							l0：0：wait：/etc/rc.d/rc.sysinit
								启动或关闭/etc/rc.d/rc3.d/目录下的服务脚本所控制服务；
									K*：要停止的服务，K##*，优先级，数字越小，越是优先关闭，依赖的服务先关闭，而后关闭被依赖的；
									S*：要启动的服务；S##*，优先级，数字越小，越是优先开启，被依赖的服务先开启，而依赖的服务后启动；
								rc脚本：接受一个运行级别数字为参数；
									脚本框架：
										for srv in /etc/rc.d/rc#.d/K*；do
											$srv stop
										done
										for srv in /etc/rc.d/rc#.d/S*；do
											$srv start
										done
								/etc/init.d/rc.d/init.d/* 脚本执行方式：
									#/etc/init.d/SRC_SCRIPT {start|stop|restart|status}
								chkconfig命令：管控/etc/init.d/每个脚本的各级别下的启动或关闭状态；
									查看：chkconfig --list [name]
									添加：chkconfig --add name
										能被添加的服务脚本定义格式之一：
										#！/bin/bash
										#
										#chkconfig:LLL NN NN
										#Description
									删除：chkconfig --del name
									修改指定的链接类型：
										chkconfig [--level LECELS] name <on|off|reset> --level LEVELS：指定要控制的级别；默认为2345；
							注意：正常级别下，最后启动的一个服务S99local没有链接至/etc/init.d下的某脚本，而是链接至了/etc/rc.d/rc.local（/etc/rc.local）脚本；因此，不见货不需要为服务脚本的程序期望能开机自动运行时，纸质放置此脚本文件中即可；
							tty1:2345：respawn：/usr/sbin/mingetty tty1
							…
							tty6:2345：respawn：/usr/sbin/mingetty tty6
								（1）mingetty会调用login程序；
								（2）打开虚拟终端的程序除了mingetty之外，还有诸如getty等；
						系统初始化脚本：/etc/rc.d/rc.sysinit
							（1）设置主机名；
							（2）设置欢迎信息；
							（3）激活udev和selinux；
							（4）挂载/etc/fstab文件中定义的所有文件系统；
							（5）检测根文件系统，并以读写方式重新挂载根文件系统；
							（6）设置系统时钟；
							（7）根据/etc/sysctl.conf文件来设置内核参数
							（8）激活lvm及raid设备；
							（9）激活swap设备；
							（10）加载额外设备的驱动程序；
							（11）清理操作；
				总结（用户空间 的启动流程）：/sbin/init：(/etc/inittab)
					设置默认运行级别 --> 运行系统初始化脚本，完成系统初始化 --> 关闭对应级别下需要停止的服务，启动对应级别下需要开启的服务 --> 设置登录终端[--> 图形终端]
			CentOS6：
				init程序：upstart，但依然为/sbin/init，其配置文件：
					/etc/init/*.conf，/etc/inittab（仅用于定义默认运行级别）
						注意：*.conf为upstart风格的配置文件；
						rcS.conf
						rc.conf
						start-tty.conf
			CentOS7：
				init程序：systemd，配置文件：/usr/lib/systemd/system/*，/etc/systemd/system/*
				完全兼容SysV脚本机制；因此，service命令依然可用；不过，建议使用systemctl命令来控制服务；
					systemctl {start|stop|restart|status} name[.service]
		博客作业：CentOS系统启动流程
	GRUB（Boot Loader）：
		grub：Grand Unified Bootloader
			grub 0.x： grub legacy
			grub 1.x：grub2
		grub legacy：
			stage1：mbr
			stage1_5：mbr之后的扇区，让stage1中的bootloader能识别stage2所在的分区上的文件系统；
			stage2：磁盘分区（/boot/grub）
			配置文件：/boot/grub/grub.conf <-- /etc/grub.conf
			stage2及内核等通常放置一个基本磁盘分区：
				功能：
					（1）提供菜单、并提供交互式接口
						e：编辑模式，用于编辑菜单；
						c：命令模式，交互式接口；
					（2）加载用户选择的内核或操作系统
						允许传递参数给内核
						可隐藏此菜单
					（3）为菜单提供了保护机制
						为编辑菜单进行认证
						为启用内核或操作系统进行认证
			如何识别GRUB设备：
				（hd#,#）
					hd#：磁盘编号，用数字表示，从0开始编号
					#：分区编号，与数字表示，从0开始编号
					（hd0,0）
			grub的命令行接口
				help：获取命令帮助列表
				help KEYWORD：详细帮助信息
				find （hd0,0）/PATH/TO/SOMEFILE；
				root（hd#，#）
				kernel /PATH/TO/KERNEL_FILE：设定本次启动时用到的内核文件；额外还可以添加许多内核支持使用的cmdline参数；
					例如：init=/path/to/init，selinux=0
				initrd /PATH/TO/INITRAMFS_FILE：设定为选定的内核提供额外文件的ramdisk；
				boot：引导启动选定的内核；
				手动在grub命令行接口下启动系统：
					root（hd#，#）
					kernel /vmlinuz-VERSION-RELEASE ro root=/dev/DEVICE
					initrd /initramfs-VERSION-RELEASE.img
					boot
			配置文件：/boot/grub/grub.cfg
				配置项：
					default=#：设定默认启动菜单项；菜单项（title）编号从0开始；
					timeout=#：指定菜单项等待选项选择的时长；
					splashimage=（hd#，#）/PATH/TO/XPM_PIC_FILE：指明菜单北京图片文件路径；
					hiddenmenu：隐藏菜单；
					password [--md5] STRING：菜单编辑认证；
					title TITLE：定义菜单项“标题”，菜单项可出现多次
						root（hd#，#）：grub查找stage2及kernel文件所在设备分区；为grub的“根”；
						kernel /PATH/TO/VMLINUZ_FILE [PARAMETERS]：启动的内核
						initrd /PATH/TO/INITRAMFS_FILE：内核匹配的ramfs文件；
						password [--md5] STRING：启动选定的内核或操作系统时进行认证；
				grub-md5-crypt命令：
			进入单用户模式：
				（1）编辑grub菜单（选定要编辑的title，而后使用e命令）
				（2）在选定的kernel后附加
					1，s，S，或single都可以；
				（3）在kernel所在行，键入“b”命令；
			安装grub：
				（1）grub-install
					grub-install --root-directory=ROOT /dev/DISK
				（2）grub
						root（hd#，#）
						setup（hd#）
			练习：
				1、新加硬盘，提供你直接单独运行bash系统；
				2、破坏grub stage1，而后在救援模式下修复它
				3、为grub设备保护功能
	Linux Kernel：
		CentOS启动流程：POST-->BootLoader(BIOS,MBR)-->kernel（initrd）-->rootfs --> switch_root --> /sbin/init
			root（hd0,0）
			kernel
			initrd
		ldd命令：
			print Shared library dependencies
			ldd [OPTIONS]…. FILE….
		内核设计体系：单内核、微内核
			Linux：单内核设计，但充分借鉴了微内核体系的设计的优点；为内核引入了模块化机制；
				内核的组成部分：
					kernel：内核核心，一般为bzlmage，通常位于/boot目录，名称为vmlinuz-VERSION-release；
					kernel object：内核对象，即内核模块，一般放置于/lib/modules/VERSION-release/
						内核模块于内核核心一般要严格匹配；
						[ ]：N
						[M]：Module
						[*]：Y，编译进内核核心
						内核：动态装载和卸载
					ramdisk：辅助性文件，并非必须，这取决ù内核是否直接驱动rootfs所在的设备；
						目标设备驱动，例如SCSI设备的驱动；
						逻辑设备驱动，例如LVM设备的驱动；
						文件系统，例如xfs文件驱动；
						ramdisk：是一个简装版的根文件系统；
			内核信息查看：
				uname命令：
					print system information
					格式：uname [OPTION] ….
						-r：内核的release号
						-n：显示当前主机名
					文件：/boot/vmlinuz-VERSION-release
				模块信息获取和管理：
					lsmod命令：
						- Show the status of modules in the Linux Kernel
						显示的内核来自于/proc/modules
					modinfo命令：
						- Show information about a Linux Kernel module
						modinfo [-0] [-F field] [-k kernel] [modulename|filename...]
							-F filed：仅显示指定字段的信息
							-n：显示文件路径；
					modprobe命令：
						- Add and remove modules from the Linux Kernel
						格式：modprobe [-r] module_name
							模块的动态装载：modprobe module_name
							模块卸载：modprobe -r module_name
					depmod命令：
						- Generate modules.dep and map files.
						内核模块依赖关系文件及系统信息映射文件的生成工具；
					模块的装载和卸载的另一组命令：
						insmod命令：
							- Simple program to insert a module into the Linux Kernel
							insmod [filename] [module options...]
								filename：模块文件的文件路径
								

​					rmmod命令：
​						rmmod [modue_name]
​			ramdisk文件的管理：
​				（1）mkinitrd命令：
​					为当前使用中的内核重新制作ramdisk文件：
​						mkinitrd [OPTION….] [<initrd-image>] <kernel-version>
​							--with=<module>：除了默认的模块之外需要转载至initramfs中的模块；
​							--preload=<module>：initramfs所提供的模块需要预先装载的模块；
​				（2）dracut命令：

   - low-level tool for generating an initramfs image
     					dracut [OPTION...] [<image> [<kernel version>]]
     		内核信息输出的为伪文件系统：
     			/proc：内核状态和统计信息的输出接口；同时，还提供了一个配置接口，/proc/sys；
     				参数：
     					只读：信息输出；例如/proc/#/*
     					可写：可接受用户指定一个“新值”来实现对某内核某功能或特性的配置；
     						/proc/sys：
     							net/ipv4/ip_forward相当于net.ipv4.ip_forward
     						（1）sysctl命令
     							专用于查看或设定/proc/sys目录下参数的值；
     								sysctl [options] [variable[=value]] [...]
     									查看：
     										sysctl -a
     										sysctl variable
     									修改其值：
     										sysctl -w variable=value
     						（2）文件系统命令
     							查看
     								cat /proc/sys/PATH/TO/KERMEL_FILE
     							设定：
     								echo “VALUES” >  /proc/sys/PATH/TO/KERMEL_FILE
     						注意：上述两种方式的设定仅当前运行的内核有效；
     						（3）配置文件/etc/sysct.conf，/etc/sysctl.d/*.conf
     							立即生效的方式：sysctl -p [/PATH/TO/KERNEL_FILE]
     					内核参数：
     						net.ipv4.ip_forward：核心转发；
     						vm.drop_caches：
     						kernel.hostname：主机名；
     						net.ipv4.icmp_echo_ignore_all ：忽略所有ping操作
     			/sys目录：
     				sysfs：输出内核识别出的硬件设备的相关属性信息，也有内核对硬件特性的可设置参数；对此些参数的修改，即可定制硬件设备的工作特性；
     				udev：通过读取/sys/目录下的硬件信息按需为各硬件创建设备文件；udev是用户空间程序；专用工具：devadmin，hotplug；
     				udev为设备创建文件时，会读取其事先定义好的规则文件，一般在/etc/udev/rules.d/目录下，以及/usr/lib/udev/rules.d/目录下；
     编译内核：
     程序包的编译安装：
     	./configure，make，make install
     前提：开发环境（开发工具，开发库）头文件：/usr/include
     开源：源代码  --> 可执行格式
     	发行版：以“通用”目标；
     前提：
     	（1）准备好开发环境；
     	（2）获取目标主机上硬件设备的相关信息，
     	（3）获取目标主机系统功能的相关信息，例如要启用文件系统；
     	（4）获取内核源代码包：www.kernel.org
     准备开发环境：
     	CentOS6,7：
     		包组：
     			Development Tools
     			Server Platform Development
     			ncureses-devel
     	CentOS7：
     		包组：
     			Development Tools
     			Server Platform Development
     			ncureses-devel
     获取目标主机上硬件设备的相关信息：
     	CPU：
     		cat /proc/cpuinfo
     		lscpu
     		x86info -a
     	PCI设备：
     		lspci
     			-v
     			-vv
     		lsusb
     			-v
     			-vv
     		lsblk
     	了解全部硬件设备信息
     		hal-device
     内核编译过程：
     	步骤：
     		tar xf linux-VERSION.tar.gz -C /usr/src
     		cd /usr/src
     		ln -sv linux-VERSION linux
     		make mconfig 配置内核选项
     		make [[-] #] 编译内核，可使用-j指定编译线程数量
     		make modules_install 安装内核模块
     		make install 安装内核
     		重新启动系统，选择使用新内核
     	screen 命令：
     		打开screen：screen
     		拆除screen：ctrl+a，d
     		列出screen：screen -ls
     		连接screen：screen -r SCREEN_ID
     		关闭screen：exit
     过程详细说明：
     	（1）配置内核选项
     		支持“更新”模式进行配置：在已有的.config文件的基础之上进行“修改配置”；
     			（a）make config：基于命令行以遍历的方式去配置内核中可配置的每个选项；
     			（b）make menuconfig：基于cureses的文本配置窗口；
     			（c）make gconfig：基于GTK开发环境的窗口界面；包组“桌面开发平台”
     			（d）make xconfig：基于QT开发环境的窗口界面；
     		支持“全新配置”模式进行配置：
     			（1）make defconfig：基于内核为目标平台提供默认的配置为模板进行配置
     			（2）make allnoconfig：所有选项均为“no”；
     	（2）编译
     		（a）多线程编译：make [-] #
     		（b）编译内核中的一部分代码：
     			（I）只编译某子目录中的相关代码
     				cd /usr/src/linux
     				make path/to/dir
     			（II）只编译一个特定的模块
     				cd /usr/src/linux
     				make path/to/dir/file.ko
     		（c）如何交叉编译：
     			目标平台与当前编译操作所在的平台不同
     				make ARCH=arc_name
     			要获取特定目标平台的使用帮助：
     				make ARCH=arc_name help
     	（3）如何在执行过编译操作系统的内核源码之上做重新编译：
     		实现清理操作：

     make clean：清理编译生成的绝大多数文件，但会保留config，及编译外部模块所需要的文件

     ​			#make mrproper：清理编译生成的所有文件，包括配置生成的config文件及某些备份文件；

     make distclean：相当于mrproper，额外清理各种patches以及编辑备份文件；

     CentOS系统安装：
     安装程序：anaconda
     	bootloader --> kernel（initrd（root）） --> anaconda
     	anaconda：
     		tui：基于cureses的文件配置窗口
     		gui：图形界面
     	CentOS的安装过程启动流程：
     		MBR：boot.cat
     		Stage2：isolinux/isolinux.bin
     			配置文件：isolinux/isolinux.cfg
     			每个对应的菜单选项：
     				加载内核：isolinux/vmlinuz
     				向内核传递参数：append initrd=initrd.img
     			装载根文件系统，并启动anaconda
     				默认界面是图形界面：512MB+内存空间；
     				若需要显示指定启动TUI接口，向启动内核传递一个参数“text”即可；
     					ESC，
     						boot：linux text
     			注意：上述内容一般为引导设备，例如可通过光盘、U盘或网络等；后续的anacona及其安装用到的程序等可以来自程序包仓库，此仓库的位置可以为：
     				本地光盘
     				本地硬盘
     				ftp server
     				http server
     				nfs server
     				如果想手动指定安装仓库：
     					ESC
     						boot：linux method
     	anaconda的工作过程：
     		安装前配置阶段：
     			安装过程的使用语言
     			键盘类型
     			安装目标存储设备
     				Basic Storage：本地磁盘
     				Special Storage：ISCSI
     			设定主机名
     			配置网络接口
     			时区
     			管理官密码
     			设定分区方式及MBR的安装位置；
     			创建一个普通用户；
     			选定要安装的程序包；
     		安装阶段：
     			在目标磁盘创建分区并执行格式化；
     			将选定的程序包安装至目标位置；
     			安装bootloader；
     		首次启动：
     			iptables
     			selinux
     			core dump
     	anaconda的配置方式：
     		（1）交互式配置按时；
     		（2）支持通过读取配置文件中事先定义好的配置项自动完成配置；遵循特定的语法格式，此文件即为kickstart文件；
     	安装引导选项：
     		boot：
     			text：文本安装方式
     			method：手动指定使用的安装方法
     			与网络相关的引导选项：
     				ip=IPADDR
     				netmask=MASK
     				gateway=GW
     				dns=DNS_SERVER_IP
     			远程访问功能相关的引导选项：
     				vnc
     				vncpassword=‘password’
     			启动紧急救援模式：
     				rescue
     			装载额外驱动：
     				dd
     			www.rehat.com/docs，《Installation guilde》
     CentOS6.X x86_64：
     	minimal install：
     安装引导选项：
     	ks：指明kickstart文件位置；
     		ks=
     			DVD drive：ks=cdrom：/PATH/TO/KICKSART_FILE
     			Hard Drive：ks=hd：/DEVICE/PATH/TO/KICKSTART_FILE
     			HTTP Server：ks=http://HOST[:PROT]/PATH/TO/KICKSTART_FILE
     			FTP Server：ks=ftp://HOST[:PROT]/PATH/TO/KICKSTART_FILE
     	kickstart文件的格式
     		命令段：
     			指定各种配置段选项，如键盘类型等
     		程序包段：
     			指明要安装的程序包，以及包组，也包括不安装的程序包；
     			%packages
     			@group_name
     			package
     			-package
     			%end
     		脚本段：
     			%pre：安装前脚本
     				运行环境：运行安装介质的微型Linux系统环境；
     			%post：安装后脚本
     				运行环境：运行完成后的系统；
     		命令段中的必备命令：
     			authconfig：认证方式配置
     				authconfig --enableshadow --passalgo=sha512
     			bootloader：定义bootloader的安装位置及相关配置
     				bootloader --append=" crashkernel=auto quiet" --location=mbr --boot-drive=sda
     			keyboard：设置键盘类型
     				keyboard --vckeymap=us --xlayouts='us'
     			lang：语言类型
     				lang en_US.UTF-8
     			part：分区布局
     				part /boot --fstype="ext4" --ondisk=sda --size=1024
     				part pv.198 --fstype="lvmpv" --ondisk=sda --size=73732
     			rootpw：管理员密码：
     				rootpw --iscrypted $6$YZWorGMBMSN1RQTY$v/c0WuuTDta/35joj9sVYRrHtgT0NDnVaKYZXbPrEa5m7n8ewL8DR6WHhC1oyefuZ.CpSE0v/pIoxge4QF90t1
     			timezone：时区
     				timezone Asia/Shanghai
     			补充：分区相关的其他指令：
     				cleanpart：清除分区
     					cleanpart --nore --drives=sda：清空磁盘分区；
     				volgroup：创建卷组
     					volgroup centos_centos7 --pesize=4096 pv.198
     				logvol：创建逻辑卷
     				logvol swap  --fstype="swap" --size=2048 --name=swap --vgname=centos_centos7
     		可选命令：
     			Install OR upgrade：安装或升级
     			text：安装界面类型，text为退、，默认为GUI
     			network：配置网络接口
     				network  --bootproto=dhcp --device=ens33 --ipv6=auto --activate
     			firewall：
     				firewall --disabled
     			selinux：Selinux
     				selinux --disabled
     			halt、poweroff或reboot：安装完成之后的行为；
     			url：指明安装时使用的repository，但为url格式；
     				url --url=172.20.0.100/cobbler/ks_mirror/CentOS-6.7-x86_64/-
     			repo：指明安装时指定的仓库
     				repo --name="CentOS" --baseurl=cdrom:sr0 --cost=100
     		参考官方文档：《Installation Guide》
     			系统安装完成之后禁用防火墙：
     				CentOS6：
     					service iptables stop
     					chkconfig iptables off
     				CentOS7：
     					systemctl stop firewalld.service
     					systemctl disable firewalld.service
     			禁用Selinux
     				编辑/etc/sysconfig/selinux或/etc/selinux/config文件，修改SELINUX参数值为下面其中之一：
     					permissive
     					disabled
     				立即生效：
     					gentenforce
     					setenforce 0
     	定制kickstart文件：
     		yum install system-config-kickstart
     		system-config-kickstart
     		检查语法错误：
     			kscalidator
     创建光盘镜像：
     	mkisofs -R -j -T -v --no-emul-boot --boot-load-size 4 --boot-table -V "CentOS 6 x86_64 boot " -C isolinux/boot.cat -b isolinux/isolinux.bin -o /root/boot.iso myboot/

