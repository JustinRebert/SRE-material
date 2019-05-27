压缩解压缩工具
	压缩比：
		目的：时间换空间
			CPU的时间 -->磁盘空间
	压缩工具：
		compress/uncompress，.z
		gzip/gunzip，.gz
		bizp2/bunzip2，.bz2
		xz/unxz，.xz
		lzma/unlzma，lzma
		zip/unzip
		tar，cpio
		（1）gzip/gunzip/zcat
			gzip, gunzip, zcat - compress or expand files
			gzip [OPTION]…. FIEL….
				-d：解压缩，相当于gunzip；
				-#：指定压缩比，默认6；数字越大压缩比越大（1-9）；
				-c：保将压缩结果输出至标准输出；
					gzip -c FILE > /PATH/TO/SOMEFILE.gz
		（2）bzip2/bunzip2/bzcat
			bzip [OPTION]…. FILE….
				-d：解压缩，相当于unbzip2；
				-#：指定压缩比，默认6；数字越大压缩比越大（1-9）；
				-k：keep，保留源文件；
		（3）xz, unxz, xzcat, lzma, unlzma, lzcat - Compress or decompress .xz and .lzma files
			lzma/unlzma/lzcat
			xz [option...]  [file...]
				-d：解压缩
				-#：指定压缩比，默认6；数字越大压缩比越大（1-9）；
				-k：keep，保留源文件；
	归档：tar，cpio
		tar - manual page for tar 1.26
			tar [OPTION...] [FILE]…
			（1）创建归档
				-c -f /PATH/TO/SOMEFILE.tar FILE….
				-cf /PATH/TO/SOMEFILE.tar FILE….
			（2）展开归档
				-xf /PATH/TO/SOMEFILE.tar
				-xf /PATH/TO/SOMEFILE.tar -C /PATH/TO/SOMDIR
			（3）查看归档文件的文件列表
				-tf /PATH/TO/SOMEFILE.tar
		归档完成后通常需要压缩，结果此前的压缩工具，就能实现压缩多个文件了；
			（4）归档压缩
				-z gzip2
					创建归档：
						zcf /PATH/TO/SOMEFILE.tar.gz FILE….
					解压缩并展开归档
						-zxf /PATH/TO/SOMEFILE.tar.gz
				-j：xz
					-jcf
					-jxf
				-J：xz
					-Jcf
					-Jxf
	zip：
		zip/unzip
			后缀名：.zip

