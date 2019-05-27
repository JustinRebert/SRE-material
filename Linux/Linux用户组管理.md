用户组管理
	Multi-tasks，Multi-Users
	每个使用者：
		用户标识、密码（3A）：
			Authentication
			Authorization
			Audition
		组：用户组，用户容器
	用户类别：
		管理
		普通用户
			系统用户
			登录用户
		用户标识：UserID，UID
			16bits二进制数字：0-65535
				管理员：0
				普通用户：1-65535
					系统用户：1-499（CentOS6），1-999（CentOS7）
					登录用户：500-60000（CentOS6），1000-60000（CentOS7）
			名称解析：名称转换
				Username<--->UID
				根据名称解析库进行：/etc/passwd
	组类别：
		管理员组
		普通用户组
			系统组
			登录组
		组标识：GroupID，GID
			管理员组：0
				普通用户组：1-65535
					系统用户组：1-499（CentOS6），1-999（CentOS7）
					登录用户组：500-60000（CentOS6），1000-60000（CentOS7）
			名称解析：groupname<--->gid
				解析库：/etc/group
			组类别2：
				用户的基本组
				用户的附加组
			组类别3：
				私有组：组名同用户名，且只包含一个用户；
				公共组：组内包含了多个用户；
		认证信息：
			通过比对事先存储的，与登录时提供的信息是否一致
			password：
				/etc/shadow
				/etc/gshadow
			密码使用策略：
				1、使用随机密码
				2、最短长度不要低于8位
				3、应该使用大写字母、小写字母、数字和标点符号至少3类
				4、定期更换；
			加密算法：
				对称加密：加密和解密使用同一个密码
				非对称加密：加密和解密使用的一对密钥；
					密钥对：
						公钥：public key
						私钥：private key
				单向加密：只能加密，不能解密；提取数据特征码；
					定长输出：
					雪崩效应：
					算法：
						md5：message digest，128bits
						sha：secure hash algorithm，160bits
						sha224
						sha256
						sha384
						sha512
					在计算之时加salt，添加的随机数；
		etc/passwd：
			name:password:UID:GID:GECOS:directory:shell
			name：用户名
			password：可以是加密的密码，也可以是占位符x；
			UID：用户ID
			GID：用户所属的主组的ID号；
			GECOS：用户的注释信息
			directory：用户的家目录
			shell：用户默认的shell，登录时默认shell程序
		/etc/shadow：
			login name：encrypted password：date of last password change：minimum password age：maximum password age：password warning period：password inactivity period：account expiration date：reserved field
			用户名：加密的密码：最近一次修改密码的时间：最短使用期限：最长使用期限：警告期段：过期期限：保留字段
		/etc/group：
			group_name:password:GID:user_list
				user_list：改组的用户成员；以此组为附加组的用户的用户列表；
	用户和组管理：
		安全上下文：
			进程以及其发起者的身份运行：
				进程对文件的访问权限，取决于发起此进程的用户的权限
		为了能够让那些后台进程或服务进程以非管理员的身份运行，通常需要为此创建多个普通用户；这类用户从不用登陆系统；
		groupadd：添加组
			groupadd [options] group
				-g GID：指定GID；默认是上一个组的GID+1
				-r：创建系统组；
		groupmod：修改组属性
			groupmod [options] GROUP
				-g GID：修改GID；
				-n new_name：修改组名；
		groupdel：删除组
			groupdel [options] GROUP
		useradd：创建用户
			useradd [options] LOGIN
				-u，--uid UID：指定UID；
				-g，--gid GROUP：指定基本组ID，必须原来已经存在
				-G, --groups GROUP1[,GROUP2,...[,GROUPN]]]：指明用户所属的附加组，读个组之间用逗号隔开；
				-c, --comment COMMENT：指明注释信息
				-d, --home-dir HOME_DIR：以指定的路径为家目录；通过复制/etc/skel此目录并重命名实现；指定的家目录如果之前已经存在，则不会为用户复制环境变量为尤物其人
				-s, --shell SHELL：指定用户的默认shell，可用的所有shell列表存储在/etc/shells文件
				-r, --system：创建系统用户；
			注意：创建用户时的许多默认配置文件为/etc/login.defs；
			useradd -D：显示出案件用户的默认配合；
			useradd -D选项：修改默认选项的值；
				修改的结果保存于/etc/default/useradd文件中
		useraddmod：
			usermod [options] LOGIN
				-u, --uid UID：修改用户的ID为此指定的新UID；
				-g, --gid GROUP：修改用户所属的基本组；
				-G, --groups GROUP1[,GROUP2,...[,GROUPN]]]：修改用户所属的附加组，原来的附加组会被覆盖；
				-a, --append：与-G一起使用，用于为用户追加新的附加组；
				-c, --comment COMMENT：修改注释信息
				-d, --home HOME_DIR：修改用户的家目录；用户原有的文件不会被转移到新的位置；
				-m, --move-home：只能与-d选项一起使用，用于将原来的家目录移动为新的家目录；
				-l, --login NEW_LOGIN：修改用户名；
				-s, --shell SHELL：修改用户的默认shell；
				-L, --lock：锁定用户密码，在用户的密码字符串之前添加一个"！"；
				-U, --unlock：解锁用户的密码；
		userdel：
			userdel [options] LOGIN
				-r, --remove：删除用户时一起删除其家目录；
		passwd：
			passwd  [-k]  [-l]  [-u  [-f]] [-d] [-e] [-n mindays] [-x maxdays] [-w warndays] [-i inactivedays] [-S] [--stdin] [username]
				（1）passwd：修改用户自己的密码；
				（2）passwd USERNAME：修改指定用户的密码，仅对root有此权限；
					-l, --lock，u, --unlock：锁定和解锁用户；
					-d, --delete：清除用户密码串；
					-e, --expire：过期期限，日期；
					-i, --inactive DAYS：非活动期限；
					-n, --minimum DAYS：密码的最短使用期限；
					-x, --maximum DAYS：密码的最长使用期限；
					-w, --warning DAYS：警告期限；
					--stdin：标准输入获取
						echo “PASSWORD”| passwd --stdin USERNAME
		gpasswd：
			组密码文件：/etc/gshadow
			gpasswd [option] group：
				-a, --add user：向组中添加用户
				-d, --delete user：从组中移除用户
		newgrp：临时切换指定的组为基本组
			newgrp [-] [group]
				-：会模拟用户重新登录以实现重新初始化其工作环境
		chage：
			chage [options] LOGIN
				-d：
				-b：
				-w：
				-m：
				-M：
		id：
			id [OPTION]... [USER]
				-u：仅显示有效的UID
				-g：仅显示用户所属的基本组ID
				-G：仅显示用户所属的所有组的ID
				-n：显示名字而非ID；
		su命令：switch user
			登录式切换：会通过读取目标用户的配置文件来重新初始化
				su - USERNAME
				su -l USERNAME
			非登录式切换：不会读取目标用户的配置文件进行初始化
				su USERNAME
			注意：管理员可无需密码切换至其他任何用户；
			-c 'COMMAND'：仅以指定用户的身份运行此处指定的命令；
		其他几个命令：chsh，chfn，finger，whoami
		命令总结：groupadd，groupmod，groupmod，useradd，usermod，userdel，passwd，gpasswd，newgrp，id，su，chage
权限管理：
	ls -l
		rwxrwxrwx
			左三位：定义user（ower）的权限
			中三位：定义group的权限；
			右三位：定义other的权限
	进程安全上下文：
		进程对文件的访问权限应用模型：
			进程的属主与文件的属主是否相同；如果相同，则应用属主权限
			否则，检查进程的属主是否属于文件的属组；如果属于这个组，则应用属组权限；
			否则，就只能应用other的权限；
	权限：
		r：read，读
		w：write，写
		x：execute，执行
		文件：
			r：可获取文件的数据
			w：可修改文件的数据
			x：可将此文件运行为进程
		目录：
			r：可使用ls命令获取其下的所有文件列表；
			w：可修改此目录下的文件列表；即创建或删除文件；
			x：可cd至此目录中，且可使用ls -l来获取所有文件的详细信息；
		mode：rwxrwxrwx
		ownership：user，group
	权限组合机制：
		---	000	0
		--x	001	1
		-w- 	010	2
		-wx 	011	3
		r-- 	100	4
		r-x 	101	5
		rw- 	110	6
		rwx 	111 	7
	权限管理命令：
		chmod：
			chmod [OPTION]... MODE[,MODE]... FILE...
			chmod [OPTION]... OCTAL-MODE FILE...
			chmod [OPTION]... --reference=RFILE FILE…
			三类用户：
				u：属主
				g：属组
				o：其他
				a：所有
			（1）chmod [OPTION]... MODE[,MODE]... FILE…
				MODE表示法：
					赋权表示法：直接操作一类用户的所有权限位
						u=
						g=
						o=
						a=
					授权表示法：直接操作一类用户的一个权限位r，w，x
						u+，u-
						g+，g-
						o+，o-
						a+，a-
			（2）chmod [OPTION]... OCTAL-MODE FILE…
			（3）chmod [OPTION]... --reference=RFILE FILE..
			常规选项：
				-R, --recursive：递归修改
		chown：
			chown [OPTION]... [OWNER][:[GROUP]] FILE...
			chown [OPTION]... --reference=RFILE FILE...
			常用选项：
				-R, --recursive：递归修改
			注意：用户仅能修改属于自己的文件的权限；
		chgrp：
			chgrp [OPTION]... GROUP FILE...
			chgrp [OPTION]... --reference=RFILE FILE…
		注意：仅管理员可修改文件的属主和属组
		umask：文件的权限反向掩码，遮罩码
			文件：
				666-umask
			文件：
				777-umask
			注意：文件用666去减，表示文件默认不能拥有执行权限，如果减得到的结果中有执行权限，则需要加一
			mask：
				umask：查看当前umask
				umask MASK：设置umask
			注意：此类设定仅对当前shell进程有效；
	install（install - copy files and set attributes）：
		单源复制：
			install [OPTION]... [-T] SOURCE DEST
		多源复制：
			install [OPTION]... SOURCE... DIRECTORY
			install [OPTION]... -t DIRECTORY SOURCE...
		创建目录：
			install [OPTION]... -d DIRECTORY...
		

​	常用选项：
​		-m, --mode=MODE：设定目标文件权限，默认为755；
​		-o, --owner=OWNER：设定目标文件的属主；
​		-g, --group=GROUP：设定文件属组
mktemp（mktemp - create a temporary file or directory）：
​	mktemp [OPTION]... [TEMPLATE]
​		常用选项：
​			-d：创建临时文件
​	注意：mktemp会创建临时文件名直接返回，因此，可直接通过命令引用保存起来；

博客作业：用户及权限管理
