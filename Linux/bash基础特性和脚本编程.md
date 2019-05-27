bash基础特性和脚本编程：
	终端，扶着在终端的接口程序：
		GUI：KDE，GNome，xfce
		CLI：/etc/shells
			bash
			zsh
			fish
	bash的特性：
		命令行展开：{}，~
		命令别名：alias，unalias
		命令历史：history
		文件名通配：glob
		快捷键：ctrl+a，e，u，k，l
		命令补全：$PATH
		路径补全：
	bash特性：命令hash
		缓存此前命令的查找结果：key-values
			key：搜索键
			values：值
		hash命令：
			hash：列出
			bash -d COMMAND：删除
			hash -r：清空
	bash特性：变量
		程序：指令+数据
			指令：有程序文件提供；
			数据：IO设备、文件、管道、变量
		程序：算法+数据结构
		变量名+指向的内存空间
		变量赋值：name=values
		数据类型：存储格式、表示数据范围、参与的运算
			编程语言：
				强类型变量：java
				弱类型变量：
					bash把所有变量统统设为字符型；
					bash中的变量无需事先声明，相当于，把声明和赋值过程同时实现；
						声明：类型，变量名
		变量替换：把变量名出现的位置替换为其所指向的内存空间中数据；
		变量引用：${var_name},$var_name
		变量名：变量名只能包含数字、字母和下划线，而且不能以数字开头；
			变量名：见名知义，命名机制遵循某种法则；不能使用程序的保留字；例如if，name，else等
		bash变量类型：
			本地变量：作用域范围仅为当前shell进程
			环境变量：作用域为当前shell进程及其子shell
			局部变量：作用域仅为某代码片段（函数上下文）
			位置参数变量：当执行脚本的shell进程传递的参数
			特殊变量：shell内置的特殊功能用的变量；
				$？：
					0：成功
					1-255：失败
			本地变量：
				变量赋值：name=values
				变量引用：${name}，$name
					""：变量名会替换为其值
					''：变量名不会替换其值
				查看变量：set
				撤销变量：unset name
					注意：此处非变量引用；
			环境变量：
				变量赋值：
					（1）：export name=value
					（2）：name=value
						export name
					（3）：declare -x name=value
					（4）：name=value
						declare -x name
				变量引用：${name},$name
				注意：bash内嵌了许多环境变量（通常为全大写），用于定义bash的工作环境
					PATH，HISTFILE，HISTFILESIZE，HISTCONTROL，SHELL，HOME，UID，PWD，OLDPWD
				查看环境变量：export，declare -x，printenv，env
				撤销环境变量：unset name
			只读变量：
				（1）declare -r name
				（2）readonly
				只读变量无法重新赋值，并且不支持撤销；存活时间为当前shell进程的生命周期，随shell进程终止而终止；
	bash特性：命令执行
		COMMAND；COMMAND1；COMMAND2；….
		逻辑运算：
			运算数：
				真（true，yes，on，1）
				假（false，no，off，0）
			与：
				1 && 1 = 1
				1 && 0 = 0
				0 && 1 = 0
				0 && 0 = 0
			或：
				1 || 1 = 1
				1 || 0 = 0 
				0 || 1 = 1 
				0 || 0 = 0 
			非：
				！1 = 0
				！0 = 1
			短路法则：
				COMMAND1 && COMMAND2
					COMMAND1位“假”，则COMMAND2不会再执行；否则，COMMAND1为真，则COMMAND2必须执行；
				COMMAND1 || COMMAND2
					COMMAND1位真，则COMMAND2不会再执行；否则，COMMAND1为假，则COMMAND2必须执行
shell脚本编程：
	编程语言的分类：根据运行方式
		编译运行：源代码-->编译器（编译）-->程序文件；
		解释运行：源代码-->运行时启动解释器，由解释器边解释边运行；
	根据其编程过程中功能的实现是调用库还是调用外部的程序文件；
		shell编程语言：
			利用系统上的命令及组件进行编程
		完整编程：
			利用库或编程组件进行编程；
	编程模型：过程是编程语言，面向对象的编程语言
		程序=指令+数据
			过程式：以指令为中心来组织代码，数据是服务于代码；
				顺序执行
				选择执行
				循环执行
				代表：C，bash
			对象式：以数据为中心来组织代码，围绕数据来组织指令；
				类（class）：实例化对象，method；
				代表：Java，C++，Python
	shell脚本编程：过程式编程，解释运行，依赖于外部命令文件运行；
		如何写脚本：
			脚本文件的第一行，顶格：给出解释器路径，用于指明解释执行当前脚本的解释器程序文件
				常见解释器：
					#!/bin/bash
					#!/usr/bin/python
					#!/usr/bin/perl
		文本编辑器：nano
			行编辑器：
			全屏编辑器：nano，vi，vim
		shell脚本是什么？
			命令的堆积；
			命令不具有幂等性，需要用程序逻辑来判断运行条件会否满足，以避免其运行错误；
		运行脚本：
			（1）：赋予执行权限，并直接运行此程序文件；
			（2）：直接运行解释器，将脚本以命令参数传递给解释器；
		注意：
			脚本中的空白行会被解释器忽略；
			脚本中除了shebang，余下所有以#开头的行，都会被视作注释行而被忽略；此即为注释行
			shell脚本的运行是通过运行一个字shell进程实现的；
	bash的配置文件：
		两类：
			profile类：为交互式登录的shell类型提供配置
			bashrc类：为非交互式登录的shell类型提供配置
		登录类型：
			交互式登录shell进程：
				直接通过某终端输入账号和密码后登录打开的shell类型
				使用su命令：su -USERNAME，或使用su -l USERNAME执行的登录切换；
			非交互式登录shell进程：
				su USERNAME 执行的登录切换
				图形界面下打开的额终端；
				运行脚本
		profile类：
			全局：对所有用户都生效；
				/etc/profile
				/etc/profile.d/*.sh
			用户个人：仅对当前用户有效；
				~/.bash_profile
			功能：
				1、定义环境变量；
				2、运行命令或脚本；
		bashrc类：
			全局：
				/etc/bashrc
			用户个人：
				~/.bashrc
			功能：
				1、定义本地变量；
				2、定义命令别名；
		注意：仅管理员可修改全局配置文件；
		交互式登录shell进程：
			/etc/profile --> /etc/profile.d/* --> ~/.bashrc --> /etc/bashrc
		非交互式登录shell进程：
			~/.bashrc --> /etc/profile-->/etc/profile.d/*
		命令行中定义的特性，例如变量和别名作用域为当前shell进程的生命周期；
		配置文件定义的特性，只对随后新启动的shell进程有效；
		让通过配置文件的特性立即生效：
			（1）通过命令行重复定义一次
			（2）让shell进程重新读取配置文件：
				source /PATH/TO/CONF_FILE
				. /PATH/TO/CONF_FILE
	算术运算：
		+，-，*，/，**，%
		算术运算：
			（1）let Var=算术运算表达式
			（2）VAR=$[算术运算表达式]
			（3）VAR=$((算术运算表达式))
			（4）VAR=$(expr$ARG1 $OP $ARG2) 
			注意：乘法符号在某些场景中需要使用转义符；
bash编程：
	脚本文件的格式：
		第一行，顶格：#/bin/bash
		注释信息：#
		代码注释：
		缩进，湿度添加空白行；
	语言：编程语法格式，库，算法和数据结构
	编程思想：
		问题空间-->解空间
	变量：
		局部变量
		本地变量
		环境变量
		位置参数变量
		特殊变量
	数据类型：字符型、数值型
		弱类型：字符型
	算术运算：
		+、-、*、/、**
		let VAR=expression
		VAR=$[expression]
		VAR=$((expression))
		VAR=$(expr argu1 argu2 argu3)
		注意：有些是否乘法符号需要转义；
		增强型赋值：
			变量做某种算术运算后回存至此变量中；
				let i=$i+1
				let i+=#
				+=、-=、*=、/=、%=
				自增：
					VAR=$[$VAR+1]
					let VAR+=1
					let VAR++
				自减：
					VAR=$[VAR-1]
					let VAR-=1
					let VAR--
	条件测试：
		判断某需求是否满足，需要有测试机制来实现；
		如何编写测试表达式以实现所需的测试；
			（1）执行命令，并利用命令的状态返回值来判断状态返回值来判断；
				0：成功
				1-255：失败
			（2）测试表达式
				test EXPRESSION
				[ EXPRESSION ]
				[[ EXPRESSION ]]
				注意：EXPRESSION两端必须有空白字符，否则为语法错误；
		bash的测试类型：
			数值测试
			字符串测试
			文件测试
			数值测试：数值比较
				-eq：是否等于；[ $num1 -eq $num2 ]
				-ne：是否不等于；
				-gt：是否大于；
				-ge：是否大于等于
				-lt：是否小于；
				-le：是否小于等于；
			字符串测试：
				==：是否等于；
				>：是否大于；
				<：是否小于
				！=：是否不等于
				=~：左侧是以字符串是否能够被右侧的PATTERN所模式匹配
				-z “STRING”：判断指定的字符串是否为空；空则为真，不空则为假；
				-n “STRING”：判断指定的字符串是否不空，不空则真，空则为假；
				注意：
					（1）字符串要加引号
					（2）要使用[[]]；
			文件测试：
				存在性测试
					-a FILE
					-e FILE
						文件的存在性测试，存在则为真，否则则为假
				存在性及类型测试
					-b FILE：是否存在并且为块设备文件；
					-c FILE：是否存在并且为字符设备文件
					-d FILE：是否存在且为目录文件
					-f FILE：是否存在且为普通文件
					-h FILE或-L FILE：是否存在并且为符号链接文件；
					-p FILE：是否存在且为命令管道文件；
					-S FILE：是否存在且为套接字文件；
				文件权限测试：
					-r FILE：是否存在且可读
					-w FILE：是否存在且可写
					-x FILE：是否存在且可执行
				特殊权限测试：
					-g FILE：是否存在且拥有sgid权限；
					-u FILE：是否存在且拥有suid权限；
					-k FILE：是否存在且用于sticky权限；
				文件是否有内容：
					-s FILE：是否有内容；
				时间戳：
					-N FILE：文件自从上一次读操作是否被修改过；
				从属关系测试：
					-O FILE：当前用户是否为文件的属主；
					-G FILE：当前用户是否为文件的属组；
				双目测试：
					FILE1 -ef FILE2：FILE1与FILE2是否为指向同一个文件系统上相同inode的硬链接；
					FILE1 -nt FILE2：FILE1是否新于FILE2；
					FILE1 -ot FILE2：FILE1是否旧于FILE2；
				组合测试条件：
					逻辑运算：
						第一种方式：
							COMMAND1 && COMMAND2
							COMMAND1 || COMMAND2
							！COMMAND
							[ -O FILE ] && [ -r FILE ]
						第二种方式：
							EXPRESSION1 -a EXPRESSION2
							EXPRESSION1 -o EXPRESSION2
							！EXPRESSION
							[ -O FILE -a -x FILE ]
		脚本的状态码返回值：
			默认是脚本中执行的最后一条命令的状态码返回值；
			自定义状态码；
				exit [n]:n为自己指定的状态码；
					注意：shell进程遇到exit时，即会终止，因此，整个脚本执行即为结束；
	向脚本传递参数：
		位置参数变量
		myscript argu1 agru2
			引用方式：
				$1，$2，….，${10}，${11}，….
			轮替：
				shift [n]：位置参数轮替；
	特殊变量：
		$0：脚本文件路径本身；
		$#：脚本参数的个数
		$*：所有参数
		$@：所有参数
			"hello" "hi" "to" "you"
	过程式编程语言代码执行顺序：
		顺序执行：逐条执行
		选择执行：代码存在一个或多个分支，只执行条件满足的分支
		循环执行：代码片段要执行0、1或多个来回；
		选择执行：
			单分支if语句：
				if 测试条件；then
					代码分支
				fi
			双分支if语句：
				if 测试条件；then
					条件为真时执行的分支
				else
					条件为假时执行的分支
				fi
	用户交互：
		语法检查：
			bash -n SCRIPT
		调试执行：
			bash -x SCRIPT
bash脚本编程：
	过程式编程语流程：
		顺序执行
		选择执行
		循环执行
	选择执行：
		（1）&&，||
		（2）if语句
		（3）case语句
		if语句：三种格式
			单分支的if语句
				if CONDITION；then
					if-true-分支；
				fi
			双分支的if语句
				if CONDITION;then
					if-true-分支
				else
					if-false-分支
				fi
			多分支的if语句
				if CONDITION1；then
					条件1为真分支
				elif CONDITION2；then
					条件2为真分支
				elif CONDITION3；then
					条件3为真分支
				elif CONDITIONn；then
					条件n为真分支
				else
					所有条件均不满足时的分支
				fi
				注意：即便多个条件可能同时能满足，分支只会执行其中一个，首先测试为值；
				注意：if语句可嵌套
	循环执行：将一段代码重复执行0、1或多次；
		进入条件；条件满足时才进入循环；
		退出条件；每个循环都应该有退出条件，以有机会退出循环；
		bash脚本：
			for循环
			while循环
			until循环
		for循环：
			两种格式：
				（1）遍历列表
				（2）控制变量
			遍历列表：
				for VALUES in LIST；do
					循环体
				done；
				进入条件：只要列表有元素，即可进入循环；
				退出条件：列表中的元素遍历完成；
				LIST的生成方式：
					（1）直接给出
					（2）整数列表
						（a）{start….end}
						（b）seq [start] [incremta] last
					（3）返回列表的命令
					（4）glob
					（5）变量引用
						$@，$*
						….
		while循环：
			while CONDITION；do
				循环体
				循环控制变量修正表达式
			done
			进入条件：CONDITION测试为“真”
			退出条件：CONDITION测试为“假”
		until循环：
			until CONDITION；do
				循环体
				循环控制变量修正表达式
			done
			进入条件：CONDITION测试为“假”
			退出条件：CONDITION测试为“真”
		循环控制语句：
			continue：提前结束本轮循环，直接进入下一轮循环判断；
				while COMDITION1；do
					CMD1
					….
					if CONDITION2；then
						continue
					fi
					COMn
					….
				done
			break：提前跳出循环：
				while COMDITION1；do
					CMD1
					….
					if CONDITION2；then
						break
					fi
					COMn
					….
				done
		创建死循环：
			while true；do
				循环体
			done
			退出方式：
				某个测试条件满足时，让循环体执行break命令；
			sleep命令：
				- delay for a specified amount of time
				sleep NUMBER
		while循环的特殊用法（遍历文件的行）：
			while read VARABLE；do
				循环体；
			done；< /PATH/TO/SOMEFILE
			依次读取/PATH/TO/SOMEFILE文件中的每一行，且将其赋值给VARIABLE变量；
		for循环的特殊用法：
			for（（控制变量初始化；条件判断表达式；控制变量的修正表达式；））；do
				循环体
			done
			控制变量初始化：仅在循环代码开始运行时执行一次；
			控制变量的修正语句：每轮循环结束会先进行孔子变量修正运算，而后再做条件判断；
		case语句：
			case语句的语法格式：
				case $VARABLE in
				PAT1）
					分支1
					；；
				PAT2)
					分支2
					；；
				*）
					分支n
					；；
				esac
			case支持global风格的通配符
				*：任意长度的任意字符；
				？：任意单个字符；
				[]:范围内任意单个字符
				a|b：a或b；
	函数：function
		过程式编程：代码重用
			模块化编程
			结构化编程
			把一段独立的代码段当做一个整体，并为之取一个名字；命名的代码段，此即为函数；
			注意：定义函数的代码段不会自动执行，在调用时执行，所谓调用函数，在代码中给定函数名即可；
				函数名出线的任何位置，在代码执行时，都会被替换为函数代码；
		语法一：
			function f_name {
				….函数体….
			}
		语法二：
			f_name {
				….函数体….
			}
		函数的生命周期：每次被调用时创建，返回时终止；
			其状态返回结果为函数体中运行的最后一条命令的状态结果；
			自定义状态返回值，需要使用：return
			return [0-255]
				0：成功
				1-255：失败
		函数返回值：
			函数的执行结果返回值：
				（1）使用echo或printf命令进行输出；
				（2）函数体中调用的命令的执行结果；
			函数的退出状态码：
				（1）默认取决于函数体中的执行的最后一条命令的退出状态码；
				（2）自定义：return
		函数可以接受参数：
			传递参数给函数：
				在函数体中当中，可以使用$1,$2,$3,….引用传递给函数的参数，还可以在函数中使用$*或$@引用传递的参数的个数；
				在调用函数时，在函数取名后以空白分隔符给定参数列即可，例如：testfunc arg1 arg2 arg3 ….
		变量作用域：
			局部变量：作用域是函数的生命周期；在函数结束时被自动销毁；
				定义局部变量的方法：local VARIABLE=VALUES
			本地变量：作用域是运行脚本的shell进程的生命周期，因此，其作用范围为当前shell脚本程序文件；
		函数递归调用：
			函数直接或间接调用自身
	数组：
		程序=指令+数据
			指令：
			数据：变量、文件
		变量：存储单个元素的内存空间
		数组：存储多个元素的内存空间
			数组名：整个数组只有一个名字
			数组索引：编号从0开始
				数组名[索引]，
				${ARRARY_NAME[INDEX]}
			注意：bash-4及之后的版本，支持自定义索引格式，而不仅仅是0,1,2，….数字格式；
				此类数组称为“关联数组”
		声明数组：
			declare -a NAME：声明索引数组
			declare -A NAME：声明关联数组
		数组中元素的赋值方式：
			（1）一次只赋值一个元素：
				ARRARY_NAME[INDEX]=value
			（2）一次赋值全部元素：
				ARRARY_NAME=（“VAL1”，“VAL2”，….）
			（3）只赋值特定元素；
				ARRARY_NAME=（[0]="VAL1"，[3]=“VAL2”,….）
				注意：bash 支持稀疏格式的数组
			（4）read -a ARRARY_NAME
		引用数组中的元素：${ARRARY_NAME[INDEX]}
			注意：引用时，只给数组名，表示引用下标为0的元素；
			${ARRARY_NAME[*]}：引用数组中的所有元素；
			${#ARRARY_NAME[@]}
		数组的长度（数组中元素的个数）：
			${#ARRARY_NAME[*]}
			${#ARRARY_NAME[@]}
		数组元素切片：${ARRARY_NAME[@]:offset:number}：表示去偏移量之后的所有元素；
		向非稀疏数组中追加元素：
			ARRARY_NAME[${ARRARY_NAME[*]}]=
		删除数组中的某元素：
			unset ARRARY[INDEX]
		关联数组：
			declare -A ARRARY_NAME
				ARRARY_NAME={[index_name1]=“values1”[index_name2]="values2"….}
	bash的内置字符串处理工具：
		字符串切片：
			${var：offset：number}
				取字符串的子串 
				取字符串的最右侧的几个字符：${var：-length}
					注意：冒号后必须有一个空白字符；
		基于模式取子串：
			${var#*word}：其中word是指定的分隔符；功能：自左而右，查找var变量所存储的字符串中，第一次出现的word分隔符，删除字符串开头至此分隔符之间所有字符；
			${var#*word}：其中word是指定的分隔符；功能：自左而右，查找var变量所存储的字符串中，最后一次出现的word分隔符，删除字符串开头至此分隔符之间所有字符；
			${var%word*}：其中word是指定的分隔符；功能：自左而右，查找var变量所存储的字符串中，第一次出现的word分隔符，删除字符串至字符串尾部之间所有字符；
			${var#*word}：其中word是指定的分隔符；功能：自左而右，查找var变量所存储的字符串中，最后一次次出现的word分隔符，删除字符串至字符串尾部之间所有字符；
		查找替换：
			${var/pattern/substi}：查找var所表示的字符串中，第一次被pattern所匹配到的字符串，将其替换为SUBSTI所表示的字符串；
			${var//pattern/substi}：查找var所表示的字符串中，所有被pattern所匹配到的字符串，将其全部替换为SUBSTI所表示的字符串；
			${var/#pattern/substi}：查找var所表示的字符串中，行首被pattern所匹配到的字符串，将其替换为SUBSTI所表示的字符串；
			${var/%pattern/substi}：查找var所表示的字符串中，行尾被pattern所匹配到的字符串，将其替换为SUBSTI所表示的字符串；
			注意：PATTERN中会用global风格和通配符；
		查找删除：
			$(var/pattern)：以PATTERN为模式查找var字符串中第一次的匹配，并删除它
			$(var//pattern)：以PATTERN为模式查找var字符串中所有的匹配，并删除它
			$(var/#pattern)：以PATTERN为模式查找var字符串中行首的匹配，并删除它
			$(var/%pattern)：以PATTERN为模式查找var字符串中行尾的匹配，并删除它
		字符串大小写替换：
			$（var^^）：把var中的所有小写字符转换为大写；
			$（var，，）：把var中的所有大写字符转换为小写；
		变量赋值：
			${var：-VARULE}：表示var变量为空，或未设置，name返回value，否则，则返回var值
			${var：=VARULE}：表示var变量为空，或未设置，name返回value，并将value复制给var变量，否则，则返回var值
			${var：+VARULE}：如果var变量不空，则返回value；
			${var：？VARULE_INFO}：如果var为空，或未设置，那么返回error_info为错误提示；否则，返回var值；
	信号捕捉：
		列出信号：
			trap -l
			kill -l
			man 7 signal
			trap “COMMAND” SIGNALS
			常可以进行捕捉的信号
				HUP，INT
	bash中使用ACSII颜色：
		\033[31mhello\033[0m
			##m：
				左侧#：
					3：前景色
					4：背景色
				右侧#：颜色种类
					1,2,3,4,5,6,7
			#m：
				加粗、闪烁等功能
			多种控制符、可组合使用，彼此间用分号隔开
	dialog命令可实现窗口化编程；
		个窗体空间使用方式；
		如何获取用户选择或键入的内容？
		默认，其输出信息被定向到了错误输出流；
	《高级bash编程指南》、《Linux命令行和shell脚本编程宝典》
