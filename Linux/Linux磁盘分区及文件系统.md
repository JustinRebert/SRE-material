Linux系统管理
	磁盘分区及文件系统管理
	RAID
	LVM
	网络属性管理
	程序包管理
	sed and awk
	进程查看和管理
	内核管理（编译和安装）
	系统启动流程
	定制、编译内核、busybox
	系统安装：kickstart，dhcp，pxe
	shell脚本编程：
Linux磁盘及文件系统管理：
	CPU，Memory（RAM），I/O
	I/O：Disks，Ehtercard
		Disks：持久存储数据
			接口类型：
				IDE（ata）：并口,133 MB/s
				SCSI：并口，Ultrascsi320,320MB/S，ultraSCSI640，640MB/S
				SATA：串口 6gbps
				SAS：串口，6gps
				USB：串口，480MB/S
				并口：同一线缆可以接多块设备；
					IDE：两个，主，从
				lops：lo per second
			硬盘：机械硬盘，固态硬盘；
				机械硬盘：
					track：磁道
					sector：扇区，512bytes
					cylinder：柱面
						分区划分基于柱面：
					平均寻道时间：
						500rpm，7200rpm，10000rpm，15000rpm
	Linux的哲学思想：一切接文件；
		设备类型：
			块（block）：随机访问，数据交换单位是“块”；
			字符（character）：线性访问，数据交换单位是“字符”；
		设备文件：FHS
			/dev
				设备文件：关联至设备的驱动程序；设备的访问入口；
					设备号：
						major：主设备号，区分设备类型；用于标明设备所需要的驱动程序；
						minjor：次设备号，区分同种类型下的不同的设备；是特定设备的访问入口；
					mknod：
						mknod - make block or character special files
						mknod [OPTION]... NAME TYPE [MAJOR MINOR]
							选项：
								-m MODE：创建后的设备文件的权限；
		设备文件名：ICANN
		磁盘：
			IDE：/dev/hd[a-z]
			SCSI，SATA，USB，SAS：/dev/sd[a-z]
			分区：
				/dev/sda#：
					dev/sda1，….
			注意：centos6和CentOS7系统统统将硬盘文件标识为/dev/sd[a-z]#
			引用设备的方式：
				设备文件名
				卷标
				UUID
	磁盘分区：MBR，GPT
		MBR：0 sector
			Master Boot Record
				分为三部分：
					446bytes：BootLoader，程序，引导启动操作系统的程序；
					64bytes：分区表，每16bytes标识一个分区，一共只能有4个分区；
						4主分区
						3主1扩展
							n逻辑分区
					2bytes：MBR区域的有效性标识；55AA为有效；
			主分区和扩展分区的标识：1-4
			逻辑分区：5+
	fdisk：
		1、查看磁盘的分区信息：
			fdisk -l [-u] [device...]：列出系统上的所有分区
		2、管理分区
			fdisk device
			fdisk提供了一个交互式管理分区，它有许多子命令，分别用于不同的管理功能；所有的操作均在内存中完成，没有直接同步到磁盘；直到使用w命令保存至磁盘上才生效；
			常用命令：
				n：创建新分区
				d：删除已有的分区
				t：修改分区类型
				l：查看所有已经ID
				w：保存并退出
				q：退出
				m：查看帮助信息
				p：现有的分区信息
		注意：在已经分区并且已经挂载其中某个分区的磁盘设备上创建的新分区，内核可能在创建完成后无法直接识别；
			查看：cat /proc/partitions 
			通知内核强制重读磁盘分区表：
				centos5：partprobe [device]
				centos6,7：partx，kpartx
				partx -a [device]
				kpartx -af [device]
				分区创建工具：parted，sfdisk；
	创建文件系统：
		格式化：低级格式化（分区之前进行，划分磁道）、高级格式化（分区之后对分区进行，创建文件系统）
			元数据，数据区
				元数据区：inode（index node）
					文件：大小、权限、属主属组、时间戳、数据块指针
				符号链接文件：存储数据指针的空间当中存储的是真是文件的访问路径‘
				设备文件：存储数据指针的空间中存储的设备号（major，minor）；
			bitmap index：位图索引
		VFS：virtual file system
			Linux的文件系统：ext2（无日志功能），ext3，ext4，xfs，relserfs，btrfs
			光盘：iso9660
			网络文件系统：nfs，cifs
			集群文件系统：gfs2，ocfs2
			内核及文件分布式系统：ceph
			windows文件系统：vfat，ntfs
			伪文件系统：proc，sysfs，tmpfs，hugepagefs
			unix文件系统：ufs，ffs，jfs
			交换文件系统：swap
			用户交换分布式文件系统：mogilefs，moosefs，glusterfs
		文件系统管理工具：
			创建文件系统工具：
				mkfs.ext#，mkfs.[….]
			检查及修复文件系统的工具
				fsck.ext#，mkfs.[…..]
			查看器属性的工具
				dumpe2fs，tune2fs
			调整文件系统特性：
				tune2fs
	内核及文件系统的组成部分：
		文件系统驱动：由内核提供：
		文件系统管理工具：由用户空间的应用程序提供
	ext系列文件系统管理工具：mke2fs
		mke2fs [OPTIONS] device
			-t {ext2|ext3|ext4}：指明要创建的文件系统类型
				mkfs.ext4=mkfs -t ext4=mke2fs -t ext4
			-b {1024|2048|4096}：指明文件系统的快大小；
			-L LABLE：指明卷标；
			-J：创建有日志功能的文件系统ext3； 
				mke2fs  -j =mke2fs -t ext3 =mkfs -t ext3=mkfs.ext3
			-i #：bytes-per-inode，指明inode与字节的比率；即每多少字节创建一个inode；
			-N #：直接指明要给次文件系统创建的inode的数量；
			-O [^]FEATURE：以指定的特性闯进目标文件系统；
		e2lable：卷标的查看与设定
			查看：e2lable device
			设定：e2lable device LABLE
		tune2fs：查看或修改ext系列问价那系统的某些属性
			adjust tunable filesystem parameters on ext2/ext3/ext4 filesystems；
			注意：块大小创建后不可修改
			tune2fs [OPTIONS] device
				选项：
					-l：查看超级块内容；
				修改指定文件系统的信息：
					-j：ext2-->ext3
					-L LABLE：修改卷标
					-m #：调整预留空间百分比
					-O [^]FEATHER：开启或关闭某种特性
					-o：[^]mount_optins：开启或关闭某种挂载选项
						acl
						^acl
			dumpe2fs：显示ext系列文件系统的属性信息
				dumpe2fs [-h] device
			用户实现文件系统检测工具
				因进程以外中止或系统断电崩溃等原因，导致其操作系统非正常终止时，可能会造成文件破坏，此时，应该检测并修复文件系统；建议，离线进行；
				ext系列文件系统的专业工具：
					e2fsck：check a Linux ext2/ext3/ext4 file system
					e2fsck [OPTIONS] device
						-y：对所有问题自动回答为yes
						-f：即使文件系统处于clean状态，强制进行扫描
					fsck：check and repair a Linux filesystem
						-y fstype：指明文件系统类型
							fsck -t ext4 = fsck.ext4
						-a：无需交互而自动修复所有错误；
						-r：交互式修复；
		mkfs.ext#，并且是向后兼容，但是ext2没有日志功能
		mkfs -t ext2=mkfs.ext2
		ext系列文件系统专用管理工具
		blkid：
		blkid device
		blkid -L LABLE：根据LABLE定位设备
		blkid -UUUID：根据UUID定位设备
	swap文件系统：
		Linux上的交换分区必须使用独立的文件系统，且文件系统的system ID，必须为82；
		创建swap设备：mkswap命令
			mkswap [OPTIONS] device
				-L LABEL：指明卷标
				-f：强制
	windows无法识别Linux的文件系统；因此，存储设备需要使用两种系统之间交叉时，应该使用windows和linux同时支持的文件系统fat32（vfat）；
		mkfs.vfat device
	文件系统的使用：
		首先要“挂载”：mount和umount命令
		根文件系统以外的其他文件系统想要能够被访问，都必须能够被访问，都必须通过“关联”至根文件系统上的某个目录来实现，此关联操作即为“挂载”；此目录即为“挂载点”；
			挂载点：mount_point，用于作为另一文件系统的访问入口；
				特性：
					（1）事先已经存在
					（2）应该使用违背或不会被其他进程使用到的目录；
					（3）挂载点下原有的文件将会被隐藏；
		mount命令：
			mount [-fnrsvw] [-t vfstype] [-o options] device dir
				命令选项：
					-r：readonly，只读挂载
					-w：read and write，读写挂载
					-n：设备挂载或卸载会同步更新至/etc/mtab文件中；-n用于禁止此特性；
					-t vstype：指明要挂载的设备上的文件系统类型；多数情况下可省略，此时mount会通过blkid来判断要挂载的设备的文件系统类型；
					-L LABLE：挂载时以卷标的方式指明设备；
						mount -L LABLE dir
					-U UUID：挂载时以UUID的方式指明设备；
			-o option：挂载选项
				async/sync：异步或同步操作；
				atime/noatime：文件或目录爱被访问时是否更新其时间戳；
				diratime/nodiratime：目录在被访问时是否更新其时间戳；
				remount：重新挂载；
				acl：支持使用acl功能；
					mount -o acl device dir
					tune2fs -o acl device
				ro：只读
				rw：读写
				/dev/nodev：此设备上是否循序创建设备文件；
				/exec/noexec：是否允许允许设备上的程序文件；
				auto/noauto：
				/user/nouser：是否允许普通用户挂载此文件系统
				suid/nosuid：是否允许程序文件上的suid和SGID特殊权限生效；
				default：Use default options: rw, suid, dev, exec, auto, nouser, and async.
			使用技巧：
				可以实现目录绑定至另一个目录上，作为其访问入口；
				mount --bind 原目录 目标目录
			查看当前系统所有已挂在的设备
				mount
				cat /etc/mtab
				cat /porc/mounts
			挂载光盘：
				mount -r  /dev/cdrom mount_point
				光盘设备文件：/dev/cdrom，/dev/dvd/
			挂载U盘：
				事先识别U盘的设备文件；
				挂载回环设备：mount -o loop /PATH/TO/SOME_FILE_MOUNT_POINT
		umount命令：
			umount [-dflnrv] {dir|device}…
			注意：正在被进程访问到的命令无法被卸载；
				查看被哪个或哪些进程所占用：
					lsof MOUNT_POINT
					fuser -v MOUNT_POINT
					终止所有用户正在访问某挂载点的进程
					fuser -km MOUNT_POINT
	交换分区的启用和禁用：
		创建交换分区的命令：mkswap
		启用：swapon
			swapon [OPTION] [DEVICE]
				-a：定义在/etc/fstab文件中的所有wap设备；
		禁用：swapoff
			swapoff DEVICE
	设定除根文件系统以外的其他文件系统能够自动挂载：/etc/fstab文件
		每行一个要挂载的文件系统的相关属性：
			6个字段：
				（1）要挂载的设备
					设备文件；
					LABEL
					UUID
					伪文件系统：如sysfs，proc，tmpfs等
				（2）挂载点
					swap类型的设备的挂载点为swap；
				（3）文件系统类型
				（4）挂载选项
					defaults：使用默认挂载选项
					如果要同时指明多个挂载选项，彼此间以逗号分隔；
						defaults，noatime，noexec
				（5）转储备份
					0：从不备份
					1：每天备份；
					2：每隔一天
				（6）自检次序
					0：不自检
					1：首先自检，通常只能是根文件系统可用1
					2：次级自检
					….
			mount -a：可自动挂载定义在此文件中所支持自动挂载的设备；
	磁盘查看命令：
		df：
			df [OPTION]... [FILE]…
				-l：只显示本地文件系统
				-h：以可读方式显示，human-readable
				-i：显示inode的使用状态而非blocks
		du：
			du [OPTION]... [FILE]…
				-s：sumary
				-h：human-readable
	链接文件：访问同一个文件的不同路径；
		硬链接：多个文件路径指向同一个inode的多个文件路径；
			特性：
				（1）目录不支持硬链接；
				（2）硬链接不能跨文件系统；
				（3）创建硬链接会增加inode节点计数
				注意：硬链接的大小和原文件的大小一模一样
			创建：
				ln src link_file
		符号链接：指向一个文件的路径的另一个文件路径；
			特性：
				（1）符号链接与源文件是两个各自独立的文件，各自的inode；对原文件创建符号连接不会增肌inode号
				（2）支持对目录创建符号链接，可以跨文件系统；
				（3）删除符号链接文件不影响原文件，但删除源文件，符号指定的路径即不存在，此时会变成无效链接；
				注意：符号链接的大小是其指向的文件的路径的字符串的字节数；
			创建：
				ln -s src link
				-v：verbose
		CentOS6如何使用xfs文件系统：
			安装软件：
				yum install -y xfsprogs
			创建：mkfs.xfs
			检测：fsck.xfs
	RAID：Redundant Arrays of Inexpensive（Independent） Disks
		Rerkeley：A case for Redundent Arrays of Inexpensive Disks RAID
			提高IO能力：
				磁盘并行读写；
			提高耐用性：
				磁盘冗余来实现；
			级别：多块磁盘组织在一起的工作方式有所不同；
			RAID实现的方式：
				外接式磁盘阵列：通过扩展卡提高适配能力
				内接式RAID：主板集成RAID控制器
				Software RAID：
		级别：level
			RAID-0：0，条带卷，strip；
				读写性能提升；
				可用空间：N*min(S1,S2,….)
				无容错能力
				最少磁盘数：2，2+
			RAID-1:1，镜像卷，mirror；
				读性能提升，写性能略有下降
				可用空间：1*min（S1,S2，….）
				有冗余能力
				最少磁盘数：2，2+
			RAID-2：
			RAID-3：
			RAID-4：
				1101，0110，1011
			RAID-5：
				读写性能提升
				可用空间：（N-1）*min（S1,S2，….）
				有冗余能力：1块磁盘
				最少磁盘数：3，3+
			RAID-6：
				读写性能提升
				可用空间：（N-2）*min（S1，S2，….）
				有冗余能力：2块磁盘
				最少磁盘数：4，4+
			RAID10：
				读写性能提升
				可读空间：N*min(S1,S2，….）
				有冗余能力：魅族镜像最多只能坏一块；
				最少磁盘数：4，4+
			RAID01：
			RAID50、RAID7
			JOBD：Just a Bunch Of Disks
				功能：将多个磁盘的空间合并一个大的连续空间使用：
				可用空间：sum（S1，S2，….）
		常用级别：RAID-0，RAID-1，RAID-5、RAID-10，RAID-50，JBOD
		实现方式：
			硬件实现方式
			软件实现方式
				CentOS6上的软件RAID的实现：
					结合内核中的md（multi devices）
					mdadm：模式化的工具
						语法模式： mdadm [mode] <raiddevice> [options] <component-devices>
							支持的RAID级别：LINERA，RAID，RAID1，RAID4，RAID5，RAID6，RAID10；
						模式：
							创建：-C
							装配：-A
							监控：-F
							管理：-f，-r，-a
						<raiddevice>：/dev/md#
						<component-devices>：任意块设备
						-C：创建模式
							-n #：使用#个块设备来创建此RAID；
							-l #：指明要创建的RAID的级别
							-a {yes|no}：自动创建目标RAID设备的设备文件
							-c CHUNK_SIZE：指明块大小
							-x #：指明空间盘大小
						-D：显示raid的详细信息：
							mdam -D /dev/md#
						管理模式：
							-f：标记指定的磁盘为损坏
							-a：添加磁盘
							-r：移除磁盘
						观察md的状态：
							cat /proc/mdstat
					watch命令：
						-n #：刷新间隔，单位是秒；
						watch -n# 'COMMAND'
	LVM
		LVM：Logical Volume Manager，Version：2
		dm：device mapper：将一个或多个底层设备组织成一个逻辑设备的模块；
			/dev/dm-#
		/dev/mapper/VG_NAME-LV_NAME
			/dev/mapper/vo10-root <-- /dev/VG_NAME/VG_NAME(/dev/vo10/root)
				/dev/vo10/root
		pv管理工具：
			pvs：简要显示pv信息显示
			pvdisplay：详细显示pv信息
			pvcreate /dev/DEVICE：创建pv
			pvremove 
		vg管理工具：
			vgs
			vgdisplay
			vgcreate VolumeGroupName PhysicalDevicePath PhysicalDevicePath [PhysicalDevicePath….]
			vgextend VolumeGroupName PhysicalDevicePath [PhysicalDevicePath….]
			vreduce VolumeGroupName PhysicalDevicePath [PhysicalDevicePath…..]
			vgremove
		lv管理工具：
			lvs
			lvdisplay
			lvcreate -L #[mMgGtT] -n NAME VolumeGroup
			lvremove /dev/VG_NAME/LV_NAME
		扩展逻辑卷：
			lvextend
				lvextend -L [+]#[mMgGtT] /dev/VG_NAME/LV_NAME
			resize2fs：
				resize2fs：ext系列扩展逻辑卷工具
		缩减逻辑卷：
			umount /dev/VG_NAME/LV_NAME
			e2fsck -f /dev/VG_NAME/LV_NAME
			resize2fs /dev/VG_NAME/LV_NAME #[mMgGtT]
			lvreduce -L [-] #[mMgGtT] /dev/VG_NAME/LV_NAME
			mount
		快照：
			lvcreate -L #[mMgGtT] -p r -s -n snapshot_name original_lv_name
	文件系统挂载使用：
		挂载光盘设备：
			光盘设备：
				IDE：/dev/hdc
				SATA：/dev/sr0
				符号链接文件：
					/dev/cdrom 
					/dev/cdrw
					/dev/dvd
					/dev/dvdrw
			mount -r /dev/cdrom /media/cdrom
			umount /dev/cdrom
		dd命令：covert and copy a file
			用法：dd if=/PATH/TO/FROM/SRC of=/PATH/TO/DESC
				bs=#：block size，复制单元大小；
				count=#：复制多少个
			磁盘拷贝：
				dd if=/dev/sda of=/dev/sdb
			备份MBR
				dd if=/dev/sda of=/tmp/mbr.bak bs=512 count=1
			破坏MBR
				dd if=/dev/zero of=/dev/sda bs=512 count=1
				dd if=/dev/zero of=/dev/sda bs=256 count=1
			两个特殊设备：
				/dev/null：数据黑洞
				/dev/zero：吐零机；
		博客作业：lvm基本应用，扩展及缩减实现；
