Linux系统上的文件类型：
	-：常规文件，即f；
	d：directory，目录文件；
	b：block device，块设备文件，支持以block为单位进行随机访问；
	c：character device，字符设备文件，支持以character为单位进行线性访问
		major number：主设备号，用于标识设备类型，进而确定要加密的驱动程序
		minor number：次设备号，用于标识统一类型中不同的设备；
			8位二进制；0-255
	l：symbolic link，符号链接文件；
	p：pipe，命名管道；
	s：socker，套接字文件；
基本命令：
	命令类型：
		外部命令
		shell内嵌命令
		type COMMAND：
			内部：builtin
			外部：显示为命令文件路径
				注意：命令可以有别名，别名可以用原名，此时原名被隐藏；如果要运行原命令，则需要\COMMAND
		命令别名：
			获取所有可用别名的定义：
				~]# alias
			定义别名：
				~]# alias NAME='COMMAND'
				注意：仅对当前shell进程有效
			撤销别名：
				~]# unalias NAME
	which命令：
		which - shows the full path of (shell) commands.
		which [options] [--] programname [...]
			--skip-alias：忽略别名
	whereis命令：
		whereis - locate the binary, source, and manual page files for a command
		whereis [options]  name…
			-b：仅搜索二进制程序路径
			-m：仅搜索使用手册文件路径
	who命令：
		who - show who is logged on
		who [OPTION]... 
			-b：显示系统此次启动时间
			-r：运行级别
	w命令：
		w - Show who is logged on and what they are doing.
		w [options] user [...]
bash的基础特性：
	命令历史：shell进程会其会话中保存此前用户提交执行过的命令；
		~]# history
			定制history的功能，磕头弄过环境变量设置，
				HISISIZE：shell进程可保留的命令历史的条数
				HISTFILE：持久保存命令历史的文件；
					.bash_history
				HISTFILESIZE：命令历史文件的大小；
		命令用法：
			history [-c] [-d offset] [n]
			history -anrw [filename]
			history -ps arg [arg...]
			-c：清空历史命令；
			-d：offset：删除指定历史命令
			-r：从文件读取命令至历史列表中；
			-w：把历史列表中的命令追加至历史文件中；
			history #：显示最近的#条命令；
		调用命令历史列表中的命令：
		！#：再一次执行历史列表中的第#条命令；
		！！：再一次执行上一条命令；
		！STRING：再一次执行命令历史列表中最近一个以STRING开头的命令；
			注意：命令的重复执行有时候需要依赖于幂等性；
		调用上一条命令的最后一个参数；
			快捷键：ESC，.
			字符串：！$
		控制命令历史记录的方式：
			环境变量：HISTCONTROL
				ignoredups：忽略重复的命令；
				ignorespaces：忽略以空白字符开头的命令；
				ignoreboth：以上两者同时生效；
			修改变量的值：
				NAME='VALUES'
	命令补全：
		shell程序在接收到用户执行命令的请求，分析完成之后，最左侧的字符串会被当作命令
		命令查找机制：
			查找内部命令：
			根据PATH环境变量中设定的目录，自左而右逐个搜索目录下的文件名；
		给定的打头字符串如果能惟一标识某命令程序文件，则直接补全；不能惟一标识某个命令程序文件，再击tab键一次，会给出列表
	路径补全：
		根据给定的起始路径下，以对应路径下的打头字符串来逐一匹配其实路径下的每个文件；
			tab：
				如果能惟一标识，则直接补全，否则，再一次tab，给出列表
	~：自动为用户的家目录，或指定的用户的家目录；
	{}：可承载一个以逗号分隔的路径列表，并能够将其展开为多个路径；
		例如：/tmp/{a，b}，相当于/tmp/a，/tmp/b
	命令的执行状态结果：
		bash通过状态返回值来输出结果：
			成功：0；
			失败：1-255；
		命令执行完成之后，其状态返回值保存于bash的特殊变量$？中；
		命令正常执行执行时，有的还会有命令返回值；
			根据命令及其功能不同，结果各不相同
		引用命令的执行结果：
			$（COMMAND）、`COMMAND`
	引用
		强引用：''
		弱引用：""
		命令引用：``
	快捷键：
		ctrl+a：跳转至命令行行首
		ctrl+e：跳转至命令行行尾
		ctrl+u：删除行首至光标所在处之间的所有字符；
		ctrl+k：删除光标所在处至行尾的所有字符；
		ctrl+l：清屏，相当于clear
	globbing：文件名通配（整体文件名匹配，而非部分）
		匹配模式：元字符
			*：匹配任意长度的任意字符
				pa*，*pa*，*pa，*p*a*
					pa，paa，passwd
			？：匹配任意单个字符
				pa？，？？pa，p？a，p？a？
					pa，paa，passwd
			[]：匹配指定范围内的单个字符，有几种格式：
				[a-z]，[A-Z]，[0-9]，[a-z0-9]
				[[：upper：]]：所有大写字母
				[[：lower：]]：所有小写字母
				[[：alpha：]]：所有字母
				[[：digit：]]：所有数字
				[[：alnum：]]：所有的字母和数字
				[[：spaces：]]：所有空白字符
				[[：punct：]]：所有标点符号pa[0-9][0-9]，2[0-9][0-9]
			[^]：匹配指定范围外的任意单一字符
			[^[：upper：]]
			[^0-9]
			[^[：alnum：]]
IO重定向及管道
	程序：指令+数据
		程序：IO
		可用于输入的设备：文件
			键盘设备、文件系统上的规范文件、网卡等；
		可用于输出的设备：文件
			显示器、文件系统上的常规文件、网卡等；
		程序的数据流有三种：
			输入的数据流：<-- 标准输入（stdin），键盘；
			输出的数据流：-->标准输出（stdout），显示器
			错误输出流：-->错误输出（stder），显示器
		fd：file descriptor，文件描述符
			标准输入：0
			标准输出：1
			错误输出：2
	IO重定向：
		输出重定向：>
			特性：覆盖输出
		输出重定向：>>
			特性：追加输出
		set -C
			禁止覆盖输出重定向至已存在的文件；
			此时可使用强制覆盖输出：>
		set+C
			关闭上述特性
		错误输出流重定向：2>，2>>
		合并正常输出流和错误输出流：
			（1）&>，&>>
			（2）COMMAND > /path/to/somefile 2>&1
				COMMAND >> /path/to/somefile 2>&1
		特殊设备：/dev/null
		输入重定向：<
		

​	tr命令：
​		tr [OPTION]... SET1 [SET2]
​			把输入的数据当中的字符，凡是在set1定义范围内出现的，通通对应转换为set2的字符
​		用法1：
​			tr SET1 SET2 < /PATH/FORM/SOMEFILE
​		用法2：
​			tr -d SET1 < /PATH/FROM/SOMEFILE
​		注意：不修改原文件
​	Here Document：<<
​	cat << EOF
​	cat > /PATH/TO/SOMEFILE << EOF
​	管道：连接程序，实现将前一个命令的输出直接定向后一个程序当做数据流
​		COMMAND1 | COMMAND | COMMAND3 | ….
​		tee命令：
​			COMMAND | tee /PATH/TO/SOMEFILE