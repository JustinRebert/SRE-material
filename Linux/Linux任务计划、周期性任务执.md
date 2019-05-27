Linux任务计划、周期性任务执行
	未来的某个时间点执行一次某任务：at，batch
	周期性运行某任务：crontab
		执行结果：会通过邮件发送给管理员
		netstat -tnlp
		ss -tnl
	本地电子邮件服务：
		smtp：simple mail transmission protocol
		pop3：Post Office Protocol
		imap4：Internet Mail Access Protocol
		mail命令：
			mailx - send and receive Internet mail
				MUA：Mail User Agent：用户收发邮件的工具程序
				mailx  [-S 'SUBJECT' ] username [ @hostname ]
					邮件正文的生产：
						（1）交互式输入；.单独成行可以表示正文结束；ctrl+d提交即可；
						（2）通过输入重定向；
						（3）通过管道；
	at命令：
		at [OPTION]…. TIME
			TIME：
				HH：MM [YYYY-mm-dd]
				noon，midnight，teatime
				tomrrow
				now+#
					UNIT：minutes，hours，days，OR weeks
			at的作业有队列，用单个字母表示，默认都使用a队列
			常用选项：
				-l：查看作业命令，相当于atq
				-f /PATH/TO/SOMEFILE：从指定文件中读取作业任务，而不再用交互式输入；
				-d  ：删除指定的作业，atrm；
				-c：查看作业的具体内容；
				-q QUEUT：指明队列；
			注意：作业执行结果是以邮件发送给提交作业的用户；
	batch命令：
		batch会让系统自行选择在系统资源较空闲的时间去执行指定的任务；
	周期性任务计划：cron
		服务程序：
			cronle：主程序包，提供了crond守护进程及相关辅助工具；
		确保crond守护进程（daemon）处于运行状态：
			centos7：
				systemctl status crond.sevice
					Active: active (running)
			centos6：
				service crond status
					…. is running.
		向crond提交作业的方式不同于at，它需要使用专用的配置文件，此文件有固定格式，不建议使用文本编辑器直接编辑此文件；要实验crontab命令；
			cron任务类型：
				系统cron任务：主要用于实现系统自身的维护；
					手动编辑：/etc/crontab文件
				用户cron任务：
					命令：crontab命令
			系统cron的配置文件：/etc/crontab   
				SHELL=/bin/bash
				PATH=/sbin:/bin:/usr/sbin:/usr/bin
				MAILTO=root
				

For details see man 4 crontabs

Example of job definition:

.---------------- minute (0 - 59)

|  .------------- hour (0 - 23)

|  |  .---------- day of month (1 - 31)

|  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...

|  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat

|  |  |  |  |

*  *  *  *  * user-name  command to be executed

​			注意：
​				（1）每一行定义一个周期性任务，共7个字段

   * * * *  *：定义周期性时间
           		user-name：运行任务的用户身份
           		command to be executed：任务
           	（2）此处的环境变量不同于用户登录后获取的环境变量，因此。建议命令使用绝对路径，或者自定义PATH环境变量；
           	（3）执行结果发送给MAILTO指定的用户
           用户cron的配置格式：/var/spool/cron/USERNAME
           SHELL=/bin/bash
           PATH=/sbin:/bin:/usr/sbin:/usr/bin
           MAILTO=root

           For details see man 4 crontabs

           Example of job definition:

           .---------------- minute (0 - 59)

           |  .------------- hour (0 - 23)

           |  |  .---------- day of month (1 - 31)

           |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...

           |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat

           |  |  |  |  |

           *  *  *  *  * user-name  command to be executed

           注意：
           	（1）每行定义一个cron任务，共6个字段
           	（2）此处的环境变量不同于用户登录后获取的环境变量，因此。建议命令使用绝对路径，或者自定义PATH环境变量；
           	（3）邮件发送给当前用户；
           时间表示法：
           （1）特定值：
           	给定时间点有效取值范围内的值；
           		注意：day of week和day of month一般不同时使用；
           （2）*
           	给定时间点上有效时间取值范围内的所有取值；表“每….”
           （3）离散取点：，
           	在时间上使用逗号分隔的多个值；
           	#，#，#
           （4）连续取值：-
           	在时间点上使用-连续开头和结束
           		#-#
           （5）在指定时间点上，定义步长：
           	/#：#即步长
           	注意：
           		（1）指定时间点不能被步长整除时，其意义不复存在；
           		（2）最小时间为“分钟”，想完成“秒”级命令，需要额外借助于其他机制；
           			定义成每分钟任务：而在利用脚本实现在每分钟之内，循环执行多次；
           示例：
           （1） 3 * * * *：每小时执行一次；；每小时的第3分钟；
           （2）3 4 * * 5：每周任务，每周5的4点3分；
           （3）5 6 7 * * ：每月执行一个；每月的旗号的6点5分；
           （4）7 8 9 10 *：每年执行一次；每年的10月9号8号7分；
           （5）9 8 * * 3,7：每周三和周日；
           （6）0 8,20 * * 3,7：
           （7）0 9-18 * * 1-5：工作时间内的每小时
           （8）*/5 * * * *：每5分钟执行一次某任务；
           （9）*/7 * * * *：
           crontab命令：
           crontab [-u user] [-l | -r | -e] [-i] [-s]
           	-e：编辑任务；
           	-l：列出所有任务
           	-r：移除所有任务；即删除/var/spool/cron/USERNAME
           	-i：在使用-r选项移除所有任务时提示用户确认；
           	-u user：root用户可为指定用户管理cron任务；
           注意：运行结果以邮件通知给当前用户；如果拒绝接受邮件；
           （1）COMMAND > /dev/null
           （2）COMMAND &> /dev/null
           注意：定义COMMAND时，如果命令用到%，需要对其转义；但放置于单引号中的%不用转义也可；
           思考：某任务在指定的时间因关机未能执行，下次开机会不会自动执行？
           不会！
           如果期望某时间因故未能按时执行，下次开机后无论是否到了相应时间点都要执行一次，可使用anacron实现；


