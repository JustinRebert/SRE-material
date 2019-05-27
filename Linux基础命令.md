常用命令：
	Linux文件系统：
		1、文件名名称严格区分字符大小写；
		2、文件可以使用除/以外任意字符
		3、文件名长度不能超过255个字符
		4、以.开头的文件为隐藏文件
			.：当前目录
			..：当前目录的上一级目录
				/etc/sysconfig/
					.：sysconfig
					..：/etc
	工作目录：working directory
	家目录：home
	常用命令：
		pwd：print working directory
			显示当前工作目录
		cd：change directory
			cd [/PATH/TO/SOMEDIR]
				cd：切换回家目录；
					注意：bash中，~表示家目录
				cd ~：切换回自己的家目录
				cd ~USERNAME：切换至指定用户的家目录
				cd -：在上一次所在目录与当前目录之间来回切换
					相关的环境变量
						$PWD：当前工作目录
						$OLDPWD：
		ls：list，列出指定目录下的内容：
			ls [OPTION]….  [FILE]….
				-a：显示所有文件，包括隐藏
				-A：显示除.和..之外的所有文件
				-l：--long，长格式列表，即显示文件的详细属性信息：
					-rw-------. 1 root root 1696 Jan 29 17:13 anaconda-ks.cfg
					-：文件类型，-，d，b，c，l，s，p
					rw-------
						rw-：文件属主的权限；
						---：文件属组的权限；
						---：其他用户（非属主、属组）的权限；
					1：数字表示文件被硬链接的次数；
					root：文件的属主；
					root：文件的属组；
					1696：数字表示文件的大小，单位是字节；
					Jan 29 17:13：文件最后一次被修改的时间
					anaconda-ks.cfg 文件名
				-h：--human-readable：对文件大小单位换算；换算后结果可能会丢失精确值；
				-d：查看目录自身而非内部的文件列表；
				-r：--release：逆序显示；
				-R：recursive，递归显示；
		cat：concatenate
			文本文件查看工具：
				cat /etc/fstab
				cat /etc/issue
			cat [OPTION]... [FILE]…
				-n：给显示的文本行编号；
				-E：显示行结束符$；
		tac：
			文本文件查看工具：
				tac /etc/fstab
				tac /etc/issue
			tac [OPTION]... [FILE]…
				-n：给显示的文本行编号；
				-E：显示行结束符$；
		file：查看文件内容类型；
			file [FILE]….
		echo：回显命令
			echo [SHORT-OPTION]... [STRING]…
				-n：不自动执行换行；
				-e：让转义符生效；
					\n：换行符
					\t：制表符
				STRING可以使用引号，单引号和双引号都可以使用
					单引号：强引用，变量引用不执行替换；
						~#] echo '$SHELL'
					双引号：弱引用，变量引用会被替换
						~#] echo '$SHELL'
			注意：变量引用的正规符号
				${name}
		关闭或重启命令：shutdown
			shutdown [OPTIONS...] [TIME] [WALL...]
				OPTIONS：
					-h：halt
					-r：reboot
					-c：cancel
				TIME：
					now
					hh：mm
					+m
						0
				WALL
		日期相关的命令：
			Linux：系统启动时从硬件读取日期和时间信息；读取完成以后，就不再与硬件相关联；
				系统时钟
				硬件时钟
			date：系统时钟
				显示日期时间：date [OPTION]... [+FORMAT]
					FORMAT：格式符
						%F
						%T
						%Y
						%m
						%d
						%H
						%M
						%S
						%s：从1970年1月1号（unix元年）0点0分0秒到命令执行的那一刻经过的秒数；
						….
				设定日期时间：date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]
			clock，hwclock：硬件时钟
				显示或设定硬件时钟
				hwclock [function] [option...]
				-s，--hctosys：以硬件时间为准，把系统时钟调整为与硬件时间相同
				-w，--systohc：以系统时间为准，把硬件时钟调整为与系统时间相同
			cal：日历
				cal [options] [[[day] month] year]
	课外：
		which，whereis，who，w
