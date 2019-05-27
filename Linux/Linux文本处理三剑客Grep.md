文本处理工具：
	Linux上文本处理三剑客：
		grep：文本过滤工具（模式：pattern）工具；
			grep：基本正则表达式，-E
			egrep：扩展正则表达式，-G，-F
			fgrep：不支持正则表达式
		sed：stream editor，流编辑器，文本编辑工具；
		awk：Linux上实现为gawk，文本报告生成器（格式化文本）；
		正则表达式：Regular Expression REGEXP
			由一类特殊字符及文本字符所编写的模式，其中有些字符不表示其字面意义，而是用于表示控制或通配的功能；
				分类：
					基本整个表达式：BRE
					扩展正则表达式：ERE
			元字符：\(hello[[:space:]]\+\)+
	grep：global search Regular expression and print out the line.
		作用：文本搜索工具，根据用户指定的“模式（过滤条件）”对目标文本逐行进行匹配检查；打印匹配到的行；
		模式：由正则表达式的元字符及文本字符所编写出的过滤条件；
		正则表达式引擎；
		grep [OPTIONS] PATTERN [FILE...]
		grep [OPTIONS] -e PATTERN ... [FILE...]
		grep [OPTIONS] -f FILE ... [FILE...]
		选项：
			--color[=WHEN], --colour[=WHEN]：对匹配到的文本着色后高亮显示；
			-i, --ignore-case：忽略字符串的大小写
			-o, --only-matching：仅显示匹配到的字符串本身；
			-v, --invert-match ：显示不能被模式匹配到的行；
			-E, --extended-regexp：支持使用扩展正则表达式；
			-F, --fixed-strings：固定字符串
			-q, --quiet, --silent：不显示任何信息，静默显示；
			-A #：after，后#行
			-B #：before，前#行
			-C #：context，前后各#行
		基本正则表达式：
			字符匹配：
				.：匹配任意单个字符；
				[]：匹配指定范围内的任意单个字符
				[^]：匹配指定范围外的任意单个字符
					[:digit:]、[:lower:]、[:upper:]、[:alnum:]、[:punct:]、[:space:]
			匹配次数：用在要指定其出现的次数的字符的后面，用于限制其前面字符出现的次数；
				*：匹配起前面的字符任意次；0,1次；
				.*：任意长度的任意字符
				\？：匹配前面的字符为0次或n次；及其前面的字符是可有可无的；
				\+：匹配其前面的字符一次或多次；即前面的字符要出现至少1次；
				\{n\}：匹配其前面的字符n次；
				\{m，n\}：匹配其前面的字符至少m次，至多n次；
					\{0,n\}：至多n次
					\{n，\}：至少n次
			位置锚定：
				^：行首锚定；用于模式的最左侧；
				$：行尾锚定；用于模式的最右侧；
				^PATTERN$：用于PATTERN来匹配整行；
					^$：空白行；
					^[[:space:]]$：空行或包含空白字符的行
				单词：非特殊字符组成的连续字符（字符串）都称为单词；
				\<或\b：词首锚定，用于单词模式的左侧；
				\>或\b：词尾锚定，用于单词模式的右侧；
				\<PATTERN\>：用来精确匹配完整单词；
			分组及引用：
				\(\)：将一个或多个字符捆绑在一起，当做一个整体进行处理；
				注意：分组括号中的模式匹配到的内容会被正则表达式引擎自动记录于内部的变量中，这些变量为：
					\1：模式从左侧起，第一个左括号以及与之前匹配的右括号之间的模式所匹配到的字符；
					\2：模式从左侧起，第二个左括号以及与之前匹配的右括号之间的模式所匹配到的字符；
					\3：模式从左侧起，第三个左括号以及与之前匹配的右括号之间的模式所匹配到的字符；
					….：
				后项引用：引用前面的分组括号中的模式所匹配到的字符；
	egrep：
		扩展正则表达式实现类似于基本正则表达式grep文本过滤功能：grep -E
		grep [OPTION]... PATTERN [FILE]…
			选项：
				-l，-o，-v，-q，-A，-B，-C
				-G, --basic-regexp：支持基本正则表达式；
			扩展正则表达式的元字符：
				字符匹配：
					.：任意单个字符
					[]：指定范围内的任意单个字符
					[^]：指定范围外的任意单个字符
				次数匹配：
					*：任意次，0,1或多次；
					?：0次或1次，其前的字符是可有可无的；
					+：其前面的字符至少1次；
					{m}：其前面的字符m次；
					{m，n}：至少m次，至多n次；
						{0，n}
						{m，}
				位置锚定：
					^：行首锚定
					$：行尾锚定
					\<，\b：词首锚定
					\>，\b：词尾锚定
				分组及引用：
					（）：分组；括号内的模式匹配到的字符会被记录于正则表达式引擎的内部变量中；
					后项引用：\1，\2，….
				或：
					a|b：a或者b；
						C|cat：C或者cat
						（C|c）at：cat或者Cat
	fgrep：不支持正则表达式或元字符；当唔需要用到元字符去编写模式时，使用fgrep性能更好；
文本查看及处理工具：
	wc：word count
		wc [OPTION]... [FILE]...
		wc [OPTION]... --files0-from=F
			选项：
				-l, --lines：字符串的行数
				-w, --words：字符串的词数
				-c, --bytes：字符串的字符个数
	cut：
		cut OPTION... [FILE]…
			选项：
				-d, --delimiter=DELIM：以指定的字符为分隔为分隔符；
				-f, --fields=LIST：挑选出的字段；
					#：指定的单个字段；
					#-#：连续的多个字段
					#，#：离散的多个字符
	sort：
		sort [OPTION]... [FILE]...
		sort [OPTION]... --files0-from=F
			选项：
				-n：基于数值大小而非字符进行排序；
				-t ：指定分隔符
				-k #：用于排序比较的字段；
				-r：逆序排序；
				-f：忽略字符大小写
				-u：重复的行只保留一份；
					重复行
	uniq：报告或重复的行
		uniq [OPTION]... [INPUT [OUTPUT]]
			选项
				-c：显示每行重复过的词数；
				-u：仅显示未曾重复过的行；
				-d：仅显示重复过的行；
	diff：
		diff [OPTION]... FILES
			diff /PATH/TO/OLDFILE /PATH/TO/PATH_FILE
				-u：使用unfield机制，即显示要修改的行的上下文，默认为3行；
	patch：向文件打补丁
		patch [OPTION]... [ORIGFILE [PATCHFILE]]
		patch /PATH/TO/OLD_FILE</PATH/TO/PATH_FILE
	
