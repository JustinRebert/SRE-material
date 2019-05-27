基本架构
	ansible架构
		连接方式（SSH）
		支持多类型主机
	Ansible Tower架构
		原始运维工作：
			维护工作量大
			安全隐患大
			协同性弱
			可视性差
入门
	安装ansible
		配置epel源
			yum install epel-release
		安装ansible
			yum install ansible -y
	配置管理节点与主机的连接
		ssh-keygen
		ssh-copy-id remoteuser@remoteserver
		ssh-keyscan remote-servers>> ~/.ssh/known_hosts
		ssh remoteuser@remoteserver
	ansible配置文件
		/etc/ansible/ansible.cfg ansible主配置文件
		/etc/ansible/hosts 主机目录配置文件
		简单的hosts文件配置
		192.168.26.7
		192.168.2688
		

​	具有分组的hosts文件配置
​	[webservers]
​	www.asone.ink
​	www.asone.top
​	[dbservers]
​	www.asone.com
​	www.asone.cn
​	命令格式：
​		ansible <host-pattern> [options]
​		检测安装环境
​			$ansible all -m -u root
​		执行命令
​			$ansible all -a "echo hello"
​		复制文件
​			ansible webservers -m copy -a "src=/etc/hosts dest=/tmp/hosts"
​		安装包
​			$ansible webservers -m yum -a "name=acme state=present"
​		启动服务
​			$ansible webservers -m service -a "name=httpd state=restarted"
​		并行执行
​			$ansible lb -a ” / shin/reboot”- f 10 
​		查看远程主机全部信息
​			$ansible all -m setup
​	ansible脚本管理主机
​		执行脚本的方法
​			$ansible-playbook deploy.yml
​			deploy.yml配置文件的格式及关键字
​				hosts：主机ip或主机组名
​				remote_user执行身份
​				vars：变量
​				tasks：playbook的动作
​				handers：事件处理操作模式
常用模块
​	调试与测试：
​		ping：测试远程主机是否存活，可以通过ansible连接
​		debug：用户调试模块，简单打印一些消息
​	文件类：
​		copy：从本地复制文件到远程
​		template：从本地复制文件到远程节点，并进行变量替换
​		file：设置文件属性
​	常用操作类：
​		user：管理用户账户
​		yum：yum包管理器
​		service：管理服务
​		Firewalld：管理防火墙中的服务和接口
​	执行shell命令
​		shell：在节点上执行shell命令，支持$HOME
​		command：在远程节点上面执行命令，不支持$HOME

