文件查看类命令：
	分屏查看命令：
		more：
			more File
				特点：翻屏至文件尾部后自动退出；
		less
			less FILE：
		head：
			查看文件的前n行
				head [OPTION]... [FILE]…
					-n #
					#
		tail：
			查看文件后的n行：
				head [OPTION]... [FILE]…
					-n #
					#
					-f：查看文件尾部内容结束后不退出，跟随显示新增加的行
		stat：
			stat - display file or file system status
			stat [OPTION]... FILE…
			时间戳：
				Access: 2019-02-19 08:39:42.347907514 +0800
				Modify: 2019-02-19 08:39:42.347907514 +0800
				Change: 2019-02-19 08:39:42.347907514 +0800
			文件：两类数据：
				元数据：metadata
				数据：data
		touch：
			touch - change file timestamps
			touch [OPTION]... FILE…
				-c：指定的文件路径不存在时，不予创建；
				-a：仅修改access time：
				-m：修改modify time：
				-t STAMP
					[[CC]YY]MMDDhhmm[.ss]
文件管理命令：
	cp：copy
		源文件； 目标文件；
		单源复制：cp [OPTION]... [-T] SOURCE DEST
		多源复制：cp [OPTION]... SOURCE... DIRECTORY
				cp [OPTION]... -t DIRECTORY SOURCE…
		单源复制：cp [OPTION]... [-T] SOURCE DEST
			如果目标文件不存在；则先创建此文件，并复制源文件的数据流至DEST中
			如果存在：
				如果DEST是非目录文件；则会覆盖目录文件；
				如果DEST是目录文件；则先在DEST目录下创建一个源文件同名的文件，并复制其数据流
		多源复制：cp [OPTION]... SOURCE... DIRECTORY
				cp [OPTION]... -t DIRECTORY SOURCE…
			如果DEST不存在：错误
			如果DEST存在：
				如果DEST是非目录文件：错误；
				如果DEST是目录文件：分别㢟每个文件至目标目录中，并保持原名；
		常用选型：
			-i：交互复制，即覆盖它前提醒用户确认；
			-f：强制覆盖目标文件；
			-r：递归复制目录；
			-d：复制符号链接本身，而非指向的源文件；
			-a：-dR --preserve=all，archive，用于实现归档；
			--perserv=
				mode：权限
				owership：属主和属组
				timestamp：时间戳
				context：安全标签
				xattr：扩展属性
				links：符号链接
				all：上述所有属性
	mv：
		mv [OPTION]... [-T] SOURCE DEST
		mv [OPTION]... SOURCE... DIRECTORY
		mv [OPTION]... -t DIRECTORY SOURCE...
		常用选型：
			-i：交互式；
			-f：force
	rm命令：remove
		rm [OPTION]... FILE…
		常用选型：
			-i：interactive
			-f：force
			-r：recursive
			删除目录：/PATH/TO/DIR
				危险操作：rm -rf /*
		注意：所有不用的文件建议不要直接删除，而是移动至某个专用目录：（模拟回收站）
	变量：
		命名的内存空间：
			变量类型：
				字符型：
				数值型：
					精确数值：
					近似数值：
				存储格式、数据范围、参与运算；
		变量的赋值操作：
			name=tom
	mkdir（make directory）：
		mkdir [OPTION]... DIRECTORY…
			-p：自动按需创建父目录：
			-v：verbose，显示详细过程
			-m：MODE：直接给定权限；
			注意：路径基名为命令的操作对象；基名之前的路径必须得存在
	rmdir（remove directory）：
		mkdir [OPTION]... DIRECTORY…
			-p：删除某空目录后，如果其父目录为空，则一并删除
			-v：显示过程
	基本命令：
		tree命令：
			tree [OPSTIONS] [directory ...]
				-L level：指定要显示的层级

