Linux程序包管理
	概述
		API：Application Programma Interface
		ABI：Application Binary Interface
			Unix-like：
				ELF
			windows
				exe，msi
			库级别的虚拟化：
				Linux：WinE
				Windows：Cywin
		系统级开发：
			C/C++：httpd，vsftpd，nginx
			go
		应用级开发：
			Java/Python/perl/ruby/php
				Java：hadoop，hbase
				Python：OpenStack
				perl：（perl）
				ruby：（ruby）
				php：（php）
		C/C++程序格式：
			源代码：文本格式的程序代码；
				编译开发环境：编译器、头文件、开发库
			二进制格式：文本格式的程序代码-->编译器-->二进制（二进制程序，库文件、配置文件、帮助文件）
		JAVA/Python程序格式：
			源代码：编译成能够在其虚拟机（jvm/pvm）运行的格式；
				开发环境：编译器、开发库
			二进制
		项目构建工具：
			C/C++：make
			java：maven
	程序包管理器：
		源代码-->目标二进制格式（二进制程序，库文件、配置文件、帮助文件）-->组织成为一个或有限几个“包”文件；
			安装、升级、卸载、查询、校验
		程序包管理器：
			Debian：dpt，dpkg，”deb”
			RadHat：reahat package manager，rpm，“.rpm“；rpm is package manager；
			S.u.S.E：rpm，”.rpm“
			Gentoo：ports
			ArcLinux：
		源代码：name-VERSION.tar.gz
			VERSION：marjor.minor.release
		rpm包命令格式：
			name-VERSION-release.arch.rpm
				VERSION：marjor.minor.release
				release.arch：rpm包的发行号
					release.os：2.el7.i386.rpm
					archerecture：i386，x64(arm64)，ppc，noarch
				redis-3.0.2.tar.gz --> redis-3.0.2.centos7.x64.rpm
			拆包：
				主包和支包
					主包：name-VERSION-release.arch.rpm
					支包：name-function-VERSION-release.arch.rpm
						function：devel，utils，libs，….
		依赖关系：
			X，Y，Z
				X-->Y，Z
					Y-->A，B，C
					C-->Y
			前端工具：自动解决依赖关系；
				yum：rpm包管理的前端工具；
				apt-get（apt-cache）：deb包管理器的前端工具；
				zypper：suse的rpm管理器前端工具；
				dnf：fedora 22+系统上rpm包管理器的前段工具；
		程序包管理器：
			功能：将编译好的应用程序的各组成文件打包成一个或几个程序包文件，从而更方便地实现程序包的安装、升级、卸载和查询管理操作；
			1、程序包的组成清单（每个程序都单独实现）；
				文件清单
				安装或卸载时运行的脚本
			2、数据库（公共）
				程序包的名称和版本
				依赖关系
				功能说明；
				安装生成的各文件的文件路径及校验码信息；
				等
				/var/lib/rpm
	获取程序包的途径：
		（1）系统发行版的光盘或官方的文件服务器（或镜像站点）：
			http://mirrors.aliyun.com,
			http://mirrors.sohu.com，
			http://mirrors.163.com
		（2）项目的官方站点：
		（3）第三方组织：
			（a）EPEL：
			（b）搜索引擎
				http://pkgs.org
				http://rpmfind.net
				http://rpm.pbone.net
		（4）自己动手，丰衣足食
		建议：检查其合法性
			来源合法性；
			程序包的完整性
	centos系统上rpm命令管理程序包：
		安装、升级、卸载、查询和校验、数据库维护
		rpm命令：rpm [OPTIONS] [PACKAGE_FILE]
			安装：-l，--install
			升级：-U，--update，-F，--freshen
			卸载：-e，--erase
			查询：-q，--query
			校验：-V，--verify
			数据库维护：--bullddb，--intdb
		安装：
			rpm {-i|--install} [install-options] PACKAGE_FILE …
				rpm -ivh PACKAGE_FILE….
				GENERAL OPTIONS：
					-v：verbose，详细信息
					-vv：更详细的输出
				[install-options]：
					-h：hash marks输出进度条；每个#表示2%的进度
					--test：测试安装，检查并报告依赖关系及冲突消息等；
					--nodeps：忽略依赖关系；不建议；
					--replacepkgs：重新安装
					注意：rpm可以自带脚本；
						四类：
							preinstall：安装过程之前运行的脚本，%pre，-nopre
							postinstall：安装过程之后运行的脚本：%post，--nopost
							preuninstall：卸载过程中开始执行之前运行的脚本。%preun，--nopreun
							postuninstall：卸载过程完成之后运行的脚本，%postun，--nopostun
					--nosignature：不检查包签名信息，不检查来源合法性；
					--nodigest：不检查包完整性信息；
		升级：
			rpm {-U|--upgrade} [install-options] PACKAGE_FILE …
			rpm {-F|--freshen} [install-options] PACKAGE_FILE …
				-U：升级或安装；
				-F：升级
				rpm -Uvh PACKAGE_FILE….
				rpm -Fvh PACKAGE_FILE….
					--oldpackage：降级安装
					--force：强制升级；
				注意：
					（1）不要对内核做升级操作；Linux支持多内核并存，因此，直接暗转新版本内核；
					（2）如果某原程序包的配置文件安装后曾被修改过，升级时，新版本的程序提供一个配置文件不会覆盖原来的配置，而是把原来的配置文件重命名（FILENAME.rpmfile）后配置；
		卸载：
			rpm {-e|--erase} [--allmatches] [--justdb] [--nodeps] [--noscripts] [--notriggers] [--test] PACKAGE_NAME ...
				-allmatches：卸载所有匹配指定名称的程序包的各版本；
				--nodeps：忽略依赖关系
				--test：测试卸载try run模式
		查询：
			rpm {-q|--query} [select-options] [query-options]
			[select-options]
				PACKAGE_NAME：查询指定的程序包收已经安装，及其安装版本；
				-a，--all：查询已经安装过的包
				-f FILE：查询指定的文件由哪个程序包安装生成；
				-g，--group GROUP：
				-p，--package PACKAGE_FILE：用于未安装的程序包提供查询操作；
				--whatisprovides CAPABILITY：查询指定的CAPABILITY由哪个程序包提供
				--whatrequires CAPABILITY：查询指定的CAPABILITY被哪个包所依赖；
			 [query-options]
				--changelog：查询rpm包的changelog；
				-l，--list：程序安装生成的所有文件列表；
				-i，--info：程序包相关的信息，版本号，大小、所属的包组等
				-c，--configfiles：查询指定的程序包提供的配置文件；
				-d，--docfiles：查询指定的程序包提供的帮助文档；
				--provides：列出指定的程序包提供的CAPABILITY；
				-R，--requires：查询指定的程序包的依赖关系；
				--scripts：查看程序包自带的脚本片断；
			用法：
				-qi PACKAGE，-qf FILE，-qc PACKAGE，-ql PACJAGE，-qd PACKAGE
				-qpi PACKAGE_FILE，-qpl PACKAGE_FILE，-qpc PACKAGE_FILE，….
		校验：
			rpm {-V|--verify} [select-options] [verify-options]
			S file Size differs
			M Mode differs (includes permissions and file type)
			5 digest (formerly MD5 sum) differs
			D Device major/minor number mismatch
			L readLink(2) path mismatch
			U User ownership differs
			G Group ownership differs
			T mTime differs
			P caPabilities differ
		包来源合法性验证和完整性验证：
			来源合法性验证：
			完整性验证：
			获取并导入信任的包制作者来源的密钥：
				对于centos发行版来说：rpm --import /etc/pki/rpm-gpg/RPM-KEY-CentOS-#
			验证：
				（1）安装此组织签名的程序时，会自动执行验证；
				（2）手动验证：rpm -k PACKAGE_FILE
		数据库重建：
			rpm管理器数据库路径：/var/lib/rpm/
				查询操作：通过此处的数据库进行；
			获取帮助：
				rpm {--initdb|--rebuilddb} [--dbpath DIRECTORY] [--root DIRECTORY]
					--initdb：初始化数据库，当前无任何数据库可实现初始化穿件一个新的；当前有时不执行任何操作；
					--rebuilddb：重新构建，通过读取当前系统上所有已经安装过的程序包进行重新创建；
	前端包管理器：
		CentOS：yun，dnf
			C/S架构
		yum：yellow dog，Yellowdog Update Modifier
		yum repository：yum repo
			存储了众多rpm包，以及包的相关的元数据文件（放置于特定目录下：repodata）：
			文件服务器：
				ftp://
				http://
				nfs://
				file:///
		yum客户端：
			配置文件：
				/etc/yum.conf：为所有仓库提供公共配置
				/etc/yum.repos.d/*.repo：为仓库的指向提供配置
			仓库的定义：
				[repositoryid]
				name=Some name for this repository
				baseurl=url://path/to/repository/
				enabled={1 | 0}
				gpgcheck={1 | 0}
				gpgkey=URL
				enablegroups={1 | 0}
				failovermethod={roundrobin | priority}
					 默认为：roundrobin随机指定
				cost=
					默认为1000
		yum命令的用法：
			yum [options] [command] [package ...]
			        * install package1 [package2] [...]
			        * update [package1] [package2] [...]
			        * update-to [package1] [package2] [...]
			        * update-minimal [package1] [package2] [...]
			        * check-update
			        * upgrade [package1] [package2] [...]
			        * upgrade-to [package1] [package2] [...]
			        * distribution-synchronization [package1] [package2] [...]
			        * remove | erase package1 [package2] [...]
			        * autoremove [package1] [...]
			        * list [...]
			        * info [...]
			        * provides | whatprovides feature1 [feature2] [...]
			        * clean [ packages | metadata | expire-cache | rpmdb | plugins | all ]
			        * makecache [fast]
			        * groups [...]
			        * search string1 [string2] [...]
			        * shell [filename]
			        * resolvedep dep1 [dep2] [...]
			           (maintained for legacy reasons only - use repoquery or yum provides)
			        * localinstall rpmfile1 [rpmfile2] [...]
			           (maintained for legacy reasons only - use install)
			        * localupdate rpmfile1 [rpmfile2] [...]
			           (maintained for legacy reasons only - use update)
			        * reinstall package1 [package2] [...]
			        * downgrade package1 [package2] [...]
			        * deplist package1 [package2] [...]
			        * repolist [all|enabled|disabled]
			        * repoinfo [all|enabled|disabled]
			        * repository-packages <enabled-repoid> <install|remove|remove-or-reinstall|remove-or-distribution-synchronization> [package2] [...]
			        * version [ all | installed | available | group-* | nogroups* | grouplist | groupinfo ]
			        * history [info|list|packages-list|packages-info|summary|addon-info|redo|undo|rollback|new|sync|stats]
			        * load-transaction [txfile]
			        * updateinfo [summary | list | info | remove-pkgs-ts | exclude-updates | exclude-all | check-running-kernel]
			        * fssnapshot [summary | list | have-space | create | delete]
			        * fs [filters | refilter | refilter-cleanup | du]
			        * check
			        * help [command]
		显示仓库列表命令：
			repolist [all|enabled|disabled]
		显示程序包：
			list
				yum list [all | glob_exp1] [glob_exp2] [...]
				yum list available [glob_exp1] [...]
				yum list updates [glob_exp1] [...]
				yum list installed [glob_exp1] [...]
		安装程序包：
			install package1 [package2] [...]
			reinstall package1 [package2] [...]（重新安装）
		升级程序包：
			 update [package1] [package2] [...]
			downgrade package1 [package2] [...]（降级安装）
		检查可用升级：
			check-update
		卸载程序包：
			remove | erase package1 [package2] [...]
		查看程序包的简要信息
			 info [...]
		查看指定的特性（可以是某文件）是由哪个程序包所提供：
			provides | whatprovides feature1 [feature2] [...]
		清理本地缓存：
			clean [ packages | metadata | expire-cache | rpmdb | plugins | all ]
		构建缓存：
			makecache [fast]
		搜索：
			search string1 [string2] [...]
			以指定的关键字搜索程序包及summary信息；
		查看指定包的所依赖的capabilities：
			deplist package1 [package2] [...]
		查看yum事物历史：
			history  [info|list|packages-list|packages-info|summary|addon-info|redo|undo|roll‐back|new|sync|stats]
		安装及升级本地程序包：
			localinstall rpmfile1 [rpmfile2] [...] (maintained for legacy reasons only - use install)
			localupdate rpmfile1 [rpmfile2] [...] (maintained for legacy reasons only - use update)
		包组管理工具：
			groupinstall group1 [group2] [….]
			groupupdate group1 [group2] [….]
			grouplist [hidden] [groupwildcard] [….]
			groupremove group1 [group2] [….]
			groupinfo group1 [….]
		如何使用光盘当做本地yum仓库：
			（1）挂载光盘到某目录：例如/media/cdrom
				mount -r -t iso9660 /dev/cdrom /media/cdrom
			（2）创建配置文件
				[centos7]
				name=
				baseurl=
				gpgcheck=
				enabled=
			（3）
		yum的命令行选项：
			--nogpgcheck ：禁止进行gpg check；
			-y：自动回答为yes；
			-q：静默模式；
			--enablerepo=repoidglob：临时启用此处的repo；
			--disablerepo=repoidglob：临时禁用此处的repo；
			--noplugins：禁用所有插件；
		yum的repo配置文件中可用的变量：
			$releasever：当前OS的发行版的主版本号
			$arch：平台
			$YUM0-$YUM9
			https://www.mirrors.aliyun.com/centos/$releasever/$basearch/os
		构建yum仓库：
			createrepo [options] <directory>
		程序包编译安装：
			testapp-VERSION-release.src.rpm --> 安装后，使用rpmbuild命令制作格式的rpm包，而后再安装；
			源代码 --> 预处理 --> 编译（gcc） --> 汇编 --> 链接 --> 执行
			源代码组织格式：
				多文件：文件中的代码之间，很可能存在跨文件依赖关系；
				C、C++：make（configure --> Makefile.in --> makefile）
				Java：maven
				C代码编译安装三步曲：
					./configure：
						（1）通过选项传递参数，指定启用特性、安装路径等；执行时会参考用户的指定以及MakeFile.in文件生成makefile；
						（2）检查各指定依赖的外部环境；
					make：
						根据makefile文件，构建应用程序；
					make install：
				开发工具：
					autoconf：生成configure脚本
					automake：生成Makefile.in
				建议：安装前查看INSTALL，README
			开源程序源代码的获取：
				官方自建站点：
					apache.org（ASF）
					mariadb.org
				代码托管站点：
					SourceForge
					Github.com
					code.google.com
			C/C++：gcc（GNU C Complier）
			编译C源代码：
				前提：提供开发工具及开发环境
					开发工具：make，gcc等
					开发环境：开发库，头文件
						glibc ：标准库
					通过“包组”提供开发组件
						CentOS6：“Development Tools”
				第一步：configure脚本
					选项：指定安装位置、指定启用特性
					--help：获取其支持使用的选项
						选项分类：
							安装路径设定：
								--prefix=/PATH/TO/SOMEWHERE：指定默认安装位置；默认为/usr/local
								--sysconfdir=/PATH/TO/SOMEWHERE：配置文件安装位置；
							System types：
							Optional Features:
								--disable-FEATURE
								--enable-FEATURE[=ARG]
							Optional Packages:
								--with-PACKAGE[=ARG] 
								--without-PACKAGE 
				第二步：make
				第三步：make install
			安装后后的配置：
				（1）导出二进制程序目录至PATH环境变量中；
					编辑文件/etc/profile.d/NAME.sh
						export PATH=/PATH/TO/BIN:$PATH
				（2）导出库文件路径：
					编辑/etc/ld.so.conf.d/NMAE.conf
						添加新的库文件所在目录至此文件中；
					让系统重新生成缓存；
						ldconfig [-v]
				（3）导出头文件
					基于链接的方式实现；
					ln -sv
				（4）导出帮助手册
					编辑/etc/man.conf文件
						添加一个MANPATH
				示例：
				编译安装apache2.4.33：
					yum install -y apr-devel apr-util-devel pcre-devel
				第一步：configure
					./configure --prefix=/usr/local/apache2 --sysconfdir=/etc/httpd2 --disable-proxy
				第二步：make
				第三步：make install
