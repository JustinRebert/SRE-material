Linux进程和作业管理
	内核的功用：进程管理、文件系统、网络功能、内存管理、驱动程序、安全功能
	Process：运行中的程序中的一个副本；
		存在生命周期
	Linux内核存储进程的结构图：Task Struct
		多任务的task struct组件的链表：task list
	进程创建：
		init：
			父子关系
			进程：都由其父子进程创建
				fork（），clone（）
		进程优先级：
			0-139：实时优先级
				100-139：静态优先级
					数字越小，优先级越高；
				Nice值：
					-20,19
				Big O：
					O(1)，O(logo)，(n^2)，O(2^n)
		进程内存：
			Page Frame：页框，用于存储页面数据
				存储页面Page
				MMU：memory Management Unit
		IPC：Inter Process Communication
			同一主机上：
				signal
				shm：share memory
				semerphor
			不同主机上：
				rpc：remote procecure call
				socket：
	Linux内核：抢占式多任务
		进程类型：
			守护进程：在系统引导过程中启动的进程，跟终端无关的进程；
			前台进程：跟终端相关，通过终端启动的进程
				注意：也可以把前台启动的进程送往后台，以守护模式运行；
		进程状态：
			运行态：running
			就绪态：ready
			睡眠态：
				可中断：interruptable
				不可中断：uninterruptable
			停止态：暂停于内存中，但不会被调度，除非手动启动它；stopped
			僵尸态：zombie
		进程的分类：
			CPU-Bound
			IO-Bound
		《Linux内核设计与实现》、《深入理解Linux内核》、《Linux内核的架构体系》
	Linux系统进程查看及管理工具：pstree，ps，pgrep，top，htop，pmap，vmstat，dstat，kill，job，bg，fg，nohup，nice，renice，killall，….
		CentOS5：sysV Init
		CentOS6：upstart
		CentOS7：Systemd
			/sbin/init，
		pstree命令：pstree - display a tree of processes
		ps命令：
			/proc/：内核中的状态信息；
				内核参数：
					可设置其值从而调整内核运行特性的参数；/proc/sys/
					状态变量：其用于输出内核中统计信息或状态信息，仅用于查看；
				参数：模拟成文件系统类型；
			进程：
				/proc/#：
					#：PID
			ps - report a snapshot of the current processes.
				选项三种风格：
					1   UNIX options, which may be grouped and must be preceded by a dash.
					2   BSD options, which may be grouped and must not be used with a dash.
					3   GNU long options, which are preceded by two dashes.
				启动进程的方式：
					系统启动过程中自动启动：与终端无关的进程；
					用户通过终端启动：与终端相关的进程；
				选项：
					a：所有与终端相关的进程；
					x：所有与终端无关的进程；
					u：以用户为中心组织进程状态信息显示；
					常用组合之一：aux
						VSZ：虚拟内存集
						RSS：常驻内存集（Resident Size）；
						STAT：状态
							R：running
							S：Interruptable sleeping
							D：uninterruptable sleeping
							T：Stopped
							Z：zomble
							+：前台进程
							l：多线程进程
							N：低优先级进程
							<：高优先级进程
							s：session leader
						-e：显示所有进程
						-f：显示完整格式的进程信息
						常用组合之二：-ef
						-F：显示完整格式的进程信息
							C：CPU utlization
							PSE：运行在哪颗CPU之上
						-H：以层级结构显示进程的相关信息；
						常用组合之三：-eFH
						常用组合之四：o，-eo，axo
							Filed1，Filed2：自定义要显示的字段列表，各选项之间以逗号分隔；
								常用的Filed：pid，ni，pri，pcpu，sat，comm，tty，ppid，rtprio
									ni：nice值；
									pri：priority，优先级；
									rtprio：real time priority，实时优先级；
		pgrep，pkill命令：
			pgrep, pkill - look up or signal processes based on name and other attributes
			pgrep [options] pattern
				常用选项：
					-u uid（effective user）：显示指定用户的进程
					-U uid（read uid）：
					-t TERMINAL：与指定的终端相关的进程；
					-l：显示进程名
					-a：显示完整格式的进程名
					-P pid：显示此进程的子进程；
		pidof命令：
			根据进程名，取其pid；
		top命令：
			top - display Linux processes
				首部信息
					uptime信息：l命令开启或关闭
					task信息及CPU信息：t命令
					内存信息：m命令
				排序：
					P：CPU百分比排序
					M：内存占用排序
					T：根据占用CPU的累积时间排序
				退出：q
				修改刷新时间间隔s
				终止指定的进程k
				选项：
					d #：指定刷新时间间隔，默认为3秒
					-b：以批次方式显示；
					-n #：显示多少批次；
		uptime命令：显示系统时间、运行时长以及平均负载；
			负载：过去1分钟、5分钟和15分钟的平均负载；
				等待队列的进程队列长度；
		htop命令：
			选项：
				-d #：指定延迟时间间隔
				-u userName：仅显示指定用户的进程；
				-s COLLUME：以指定字段进行排序；
			子命令：
				l：显示选定的进程打开的文件列表
				s：跟踪选定的进程的系统调用；
				t：以层级关系显示各进程状态；
				a：将选定的进程绑定至某指定的CPU核心；
		vmstat命令：vmstat - Report virtual memory statistics
			vmstat [options] [delay [count]]
			选项：
				pocs：
					r：等待运行的进程的个数；CPU上等待运行的任务的队列长度
					b：处于不可中断睡眠的进程个数；被阻塞的任务队列的长度
				memory：
					swapd：交换内存的使用总量；
					free：空闲的物理内存总量；
					buffer：用于buffer的内存总量
					cache：用于cache的内存总量
				swap：
					si：数据进入swap数据的速率（kb/s）
					so：数据离开swap的速率（kb/s）
				io：
					bi：从块设备读入数据到系统速率（kb/s）
					bo：保存数据到块设备的速率（kb/s）
				system：
					in：interrupts，中断速率；
					cs：context switch，上下文切换速率；
				cpu：
					us：user space
					sy：system
					id：idle
					wa：wait
					st：stolen
			选项：
				-s：显示内存统计数据；
		pmap命令：
			- report memory map of a process
			pmap [options] pid [...]
				选项：
					-x，--extend：显示详细信息
			另一种查看方式：cat /proc/PID/maps
		glances命令：
			- A cross-platform curses-based system monitoring tool
			glances [OPTIONS]
			内建命令
				ENTER  Set the process filter pattern (as a regular expression)
				       a      Sort process list automatically
				       b      Switch between bit/s or Byte/s for network I/O
				       c      Sort processes by CPU usage
				       d      Show/hide disk I/O stats
				       e      Enable/disable top extended stats
				       f      Show/hide file system stats
				       F      Switch between file system used and free space
				       g      Generate graphs for current history
				       h      Show/hide the help screen
				       i      Sort processes by I/O rate
				       l      Show/hide log messages
				       m      Sort processes by MEM usage
				       n      Show/hide network stats
				       p      Sort processes by name
				       q      Quit
				       r      Reset history
				       s      Show/hide sensors stats
				       t      Sort process by CPU times (TIME+)
				       T      View network I/O as combination
				       u      Sort processes by USER
				       U      View cumulative network I/O
				       w      Delete finished warning log messages
				       x      Delete finished warning and critical log messages
				       z      Show/hide processes stats
				       z      Show/hide processes list (for low CPU consumption)
				       0      Task's cpu usage will be divided by the total number of CPUs
				       1      Switch between global CPU and per-CPU stats
				       2      Enable/disable left sidebar
				       3      Enable/disable the quick look module
				       4      Enable/disable all but quick look and load module
				       /      Switch between short name/command line (processes name)
				常用选项：
					-b：以Byte为单位显示网上的数据速率；
					-d：关闭磁盘I/O模块；
					-m：关闭mount模块；
					-n：关闭network模块；
					-t #：刷新时间间隔；
					-1：每个CPU的相关数据单独显示；
				C/S模式下运行glances命令：
					服务器模式：
						glances -S -B IPADR
							IPADDR：本机的某地址，用于监听
					客户端模式：
						glances -c IPADDR
							IPADDR：服务器地址，用于监听
		dstat命令：
			- versatile tool for generating system resource statistics
			dstat [-afv] [options..] [delay [count]]
			常用选项：
				-c，--cpu：显示cpu相关信息
					-C #，#，….，total
				-d，--disk：显示磁盘的相关信息
					-D sda，sdb，….，total
				-g：显示page相关的速率数据；
				-m：memory的相关统计数据
				-n：Interface的相关统计数据
				-p：显示process的相关统计数据
				-r：显示lo请求的相关统计数据
				-s：显示swapped的相关统计数据
				--tcp
				--udp
				--raw
				--socket
				--ipc
				--top-cpu：显示占用CPU的进程
				--top-io：最占用IO的进程
				--top-men：最占用内存的进程
				--top-lantency：延迟最大的进程
		kill命令：
			- terminate a process
			用于向进程发送信号，以实现对进程的管理；
			显示当前系统可用信号：
			kill -l [signal]
			每个信号的标识方法有三种；
				1）信号的数字标识 
				2）信号的完整名称；
				3）信号的简写名称；
			向进程发信号：
				kill [-s signal|-p] [-q sigval] [-a] [--] pid…
				常用信号：
					1）SIGHUP：无须关闭进程而让其重读配置文件；
					2）SIGINT：中止正在运行的进程，相当于ctrl+c
					9）SIGKILL：杀死运行中的进程
					15）SIGTERM：中止运行中的进程
		killall命令：
			- kill processes by name
			killall [-SIGNAL] program
	Linux系统作业控制：
		Job：
			前台作业：通过终端启动，且启动后会一直占据终端；
			后台作业：可以通过终端启动，但启动后姬转如后台运行（释放终端）；
		如何让作业运行在后台？
			（1）运行中的作业
				ctrl+z
				注意：送往后台后，作业会转为停止态；
			（2）尚未启动的作业
				COMMAND &
				注意：此类作业虽然被送往后台，但其依然与终端相关；如果希望把送往后台的作业剥离与终端的关系：
					nohup COMMAND &
			（3）
		查看所有作业列表：
			jobs
		可实现作业控制常用命令：
			fg [[%][JOB_NUM]：把指定的作业调回前台
			bg [[%]JOB_NUM]：把送往后台的作业在后台继续运行
			kill [[%]JOB_NUM]：终止指定的作业；
	调整进程优先级
		可通过nice优先级范围：100-139
			分别对应于-20,19
		进程启动时，其nice值默认为0，其优先级是120；
		nice命令：
			以指定的nice值启动并运行命令
				nice [OPTION] [COMMAND [ARG]...]
					选项：
						-n NICE
				注意：仅管理员可调低nice值；
		renice命令：
			renice [-n] NICE PID….
		查看NICE值和优先级：
			ps axo pid，ni，prioty，comm
	未涉及到的命令：str，tsar，lostat，lftop，nethog，….
	博客作业：htop/dstat/top/ps命令使用