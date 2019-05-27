GNU AWK：
	文本处理工具：grep，sed，awk
		grep，egrep，fgrep：文本过滤工具：PATTERN
		sed：行编辑器
			模式空间、保持空间
		awk：报告生成器，格式化文本输出工具；
		AWK：Aho，Weinberger，Kernighan --> New AWK，NAWK
		GNU awk，gawk
	gawk - pattern scanning and processing language
		基本用法：gawk [OPTIONS] 'program' FILE….
			program：PATTERN{ACTION STATEMENTS}
				语句之间分号分隔
				print，printf
			选项：
				-F：指明输入时用到的字段分隔符；
				-V var=VARIABLE：自定义变量；
		1、print
			print item1，ITem，….
			要点：
				（1）逗号分隔符；
				（2）输出的各item可以字符串，也可以是数值；当前记录的字段、变量或awk的表达式；
				（3）如果省略item，相当于print $0;
		2、变量：
			2.1 内建变量
				FS：input field seperator，默认为空白字符；
				OFS：output field Seperator，默认为空白字符；
				RS：input record seperator，输入时的换行符；
				ORS：output record seperator，输出时的换行符；
				NF：number of field，字段数量
					{print NF}，{print $NF}
				NR：number of record，行数
				FNR：各文件分别计数，行数
				FILENAME：当前文件名；
				ARGC：命令行参数的个数；
				ARGV：数组，保存的是命令行所给定的各参数；
			2.2 自定义变量
				（1）-v var=value
					变量名区分字符大小写；
				（2）在program中直接定义
		3、printf命令：
			格式化输出：printf FORMAT，item1，item2，….
				（1）FORMAT必须要给出
				（2）不会自动换行，需要显示给出换行控制符，\n
				（3）FORMAT中需要分别为后面的每个item指定一个格式化符号：
					格式符：
						%c：显示字符的ASCII码
						%d，%i：显示十进制整数
						%e，%E：科学计数法数值显示
						%f：显示为浮点数
						%g，%G：以科学计数法或浮点形式显示数值；
						%s：显示字符串；
						%u：无符号整数
						%%：显示%自身
					修饰符：
						#[.#]：第一个数字 控制显示的宽度，第二个#表示小数点后面的精度
							%3.1f
						-：左对齐
						+：显示数值的符号
		4、操作符
			算术运算操作符：
				x+y，x-y，x*y，x/y，x^y,x%y
				-x
				+x：转换为数值
			字符串操作符：没有符号的操作符，字符串连接
			赋值操作符：
				>，>=，<，<=,!=，==
			模式匹配符：
				~：是否匹配
				！~：是否不匹配
			逻辑操作符：
				&&
				||
				！
			函数调用：
				function_name（arg1，arg2，….）
			条件表达式：
				selector？if-true-expression：if-false-expression
		5、pattern：
			（1）empty：空模式，匹配每一行；
			（2）/regular EXPRESSION/：仅处理匹配到的每一行
			（3）regular expression：关系表达式，结果有“真”有“假”；结果为真才会被 处理；
			（4）line ranges：行范围
				startline，endline：/pat1/，/pat2/
				注意：不支持直接给出数字的格式
			（5）BEGIN/END模式：
				BEGIN{}，仅在开始处理文件中的文本之前执行一次；
				END{}，仅在文本处理完成之后执行一次；
		6、常用的action;
			（1） expressions：
			（2）control statments：if，while等
			（3）compound statements：组合语句；
			（4）input statements
			（5）output statements
		7、控制语句
			if（condition）{statments}
			if（condition）{statments}else{statements}
			while（condition）{statements}
			do（statements）while{condition}
			for（expr1，expr2，expr3）{statements}
			break
			continue
			delete array[index]
			delete arrary
			exit
			{statements}
			7.1 if-else
				语法：if（condition）statements [else statements ]
				使用场景：对awk取得的整行或某个字段做条件判断；
			7.2 while循环
				语法：while（condition）statements
					条件“真”，进入循环；条件”假“，退出循环；
				使用场景：对一行内的多个字段逐一雷士处理时使用，对数组中各元素逐一处理时使用；
			7.3 do-while循环：
				语法：do statements while（condition）
					意义：至少执行一次循环体
			7.4 for循环
				语法：for（expr1；expr2；expr3）statements
					for （variable assignment；condition；iteration process）{for-body}
				特殊用法：
					能够遍历数组中的元素
					语法：for（var in array）{for-body}
			7.5 switch语句：
				语法：switch （expression）{case value1 or /regexp/：statements；case value2 or /regexp2/：statements；….；
				default：statements
			7.6 break和continue
				break [n]
				continue
			7.7 next语句
				能提前结束本行的处理直接进入下一行
		8、array
			关联数组：array[index-expression]
				index-expression：
					（1）可使用任意字符串；
					（2）如果某数组元素事先不存爱，在引用时，awk会自动创建此元素，并将其值初始化为“空串”；
					若要判断数组是否存在某元素，要使用“index in array”格式进行；
				若要遍历数组中的每个元素，要使用for循环；
					for （var in array）{for-body}
					注意：var会遍历array的每个索引；
		9、函数
			9.1 内置函数
				数值处理
					rand（）：返回0和1之间一个随机数；
				字符串处理：
					length（[s]）：返回指定字符串的长度；
					sub（r，s，[t]），以r表示的模式来查找t所表示的字符中的匹配的内容，并将其第一次出现替换为s所表示的内容
					sub（r，s，[t]），以r表示的模式来查找t所表示的字符中的匹配的内容，并将其第一次出现替换为s所表示的内容
					split（s，a，[，t]）：以r为分隔符切割字符s，并将切割后的结果保存至a所表示的数组中；
			9.2 自定义函数
				《sed和awk》
