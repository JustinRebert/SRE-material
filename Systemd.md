POST--> Boot Sequene(BIOS) --> Bootloader(MBR) --> Kernel(Ramdisk) --> rootfs --> /sbin/init
	init：
		CentOS 5：SysV init
		CentOS 6：Upstart
		CentOS 7：Systemd
	Systemd的新特性：
		系统引导时实现服务并行启动；
		按需激活进程；
		系统状态快照；
		基于依赖关系定义服务控制逻辑；
	核心概念：Unit
		由其相关配置文件进行标识、识别和配置；文件中包含了与系统服务、监听的Socket、保存的快照以及其与init相关的信息；这些配置文件的主要保存在：
			/usr/lib/systemd/system
			/run/systemd/system
			/etc/systemd/system
	unit常见的类型：
		Service unit ：文件扩展名为.service，用于定义系统服务；
		Target unit：文件扩展为.target，用于模拟实现“运行级别”；
		Device unit：.device，用于定义内核识别的设备；
		Mount unit：.mount，定义文件系统挂载点；
		Socket unit：.socket，用于标识进程间通信用到的socket文件；
		Snapshot unit：.snapshot，管理系统快照
		Swap unit：.swap，用于swap设备；
		Automount unit：.automount，文件系统自动挂载设备；
		Path unit：.path，用于定义文件系统中的一个文件或目录；
	关键特性：
		基于socket的激活机制：socket与程序分离；
		基于bus的激活机制；
		基于device的激活机制；
		基于path的激活机制；
		系统快照：保存各unit的当前信息与持久存储设备中；
		向后兼容Sysv Init脚本：
			/etc/init.d/
	不兼容：
		systemctl的命令时固定不变的；
		非由systemd启动的服务，systemctl无法与之通信；
	管理系统服务：
		CentOS 7：service类型的Unit文件；
			systemctl命令：
				 - Control the systemd system and service manager
				systemctl [OPTIONS...] COMMAND [NAME...]
					启动：service NAME start ==> systemctl start NAME.service
					停止：service NMAE stop ==> systemctl stop NAME.service
					重启：service NAME restart ==> systemctl restart NAME.service
					条件式重启：service NAME condrestart ==> systemctl try-restart NAME.service
					重载或重启服务：systemctl reload-or-restart NAME.service
					重载或条件式重启服务：systemctl reload-or-try-restart NAME.service
					查看某服务当前激活与否的状态：systemctl is-active NAME.service
					查看所有已激活的服务：systemctl list-units --type） service 
					查看所有服务（已激活及未激活）：systemctl list-units -t service --all
					设置服务开机自启：chkconfig NAME on ==> systemctl enable NAME.service
					禁止服务开机自启：chkconfig NAME off ==> systemctl disable NAME.service
					查看某服务是否能开机自启：chkconfig --list NAME ==> systemctl is-enabled NAME.service
					禁止某服务设定为开机自启：systemctl mask NAME.service
					取消此禁止：systemctl unmask NAME.service
					查看依赖服务：systemctl list-dependencles NAME.service
	管理target units：
		运行级别：
			0 ==> runlevel0.target，poweroff.target
			1 ==> runlevel1.target，rescue.target
			2 ==> runlevel2.target，multi-user.target
			3 ==> runlevel3.target，multi-user.target
			4 ==> runlevel4.target，multi-user.target
			5 ==> runlevel5.target，graphical.target
			6 ==> runlevel6.target，reboot.target
		级别切换：init N ==> systemctl isolate NAME.service
		查看级别：runlevel ==> Systemctl list-units --type target
		查看所有级别：systemctl list-units -t target -a
		获取默认运行级别：systemctl get-default
		修改默认运行级别：systemctl set-default NAME.target
		切换至紧急救援模式：systemctl rescue
		切换至emergency模式：systemctl emergency
	其他常用命令：
		关机：systemctl halt，systemctl poweroff
		重启：systemctl reboot
		挂起：systemctl suppend
		快照：systemctl hibernate
		快照并挂起：systemctl hybrid-sleep
	service unit file：
		文件的组成：
			[unit]：定义与unit类型无关的通用选项；用于提供unit的描述信息、unit行为及依赖关系
			[Service]：与特定类型相关的专用选型；此处为Service类型；
			[Install]：定义由“systemctl enable”以及“systemctl disable”命令在实现服务启用或禁用到的一些选项；
		Unit段的常用选项：
			Description：描述信息；意义性描述
			After：定义unit的启动顺序；表示当前unit应该于哪些unit启动；其功能与Before相反；
			Reques：依赖到的其他units；强依赖，被依赖的units无法激活时，当前unit即无法激活；
			Wants：依赖到的其他units；弱依赖；被依赖的units无法激活时，并不妨碍当前units激活；
			conflicts：定义untis间的冲突关系；
		Service段的常用选项：
			Type：用于定义影响ExecStart及相关参数的功能的unit进程启动类型；
				类型：
					simple：
					forking：
					oneshot：
					dbus：
					notify：类似于simple，
					idle：
			EnvironmentFile：环境配置文件；
			ExecStart：指明启动unit要运行命令或脚本；ExecStartPre，ExecStartPost
			ExecStop：指明停止units要运行的命令或脚本；
			Restart：
		Install段的常用选项：
			Alias：
			RequiredBy：被哪些units所依赖；
			WantedBy：被哪些units所依赖
	注意：对于新创建的unit文件或，修改了units文件，要通知systemd重载此配置文件；
		systemctl daemon-reload
