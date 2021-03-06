Linux Service and Security
	Openssl（ssl/tls）
	openssh（ssh）
	bind（dns）
	web（httpd）：httpd（apache），php，mariadb（mysql），lamp，nginx（tengine，openresty）lnmp
	file server：nfs，samba，ftp
	dhcp，pxe
	iptables
	sudo，pam，nsswitch
openssl：
	传输层：TCP，UDP，STCP
		prot：进程地址，进程向内核注册使用某端口（独占）
	同一主机上的进程间通信：IPC，message quene，shm，semerphor
	不同主机上的进程通信：socket
		cip：port <-->sip：port
			cip：55673 <--> sip：80
			监听模式：LISTEN（ip：port）
	SSL：secure Sockets Layer
		http --> ssl --> https
	安全的目标：
		保密性：confidentially
		完整性：integerity
			数据完整性
			系统完整性 
		可用性：availability
	攻击类型：
		威胁保密性的攻击：窃听、通信量分析；
		威胁完整性的攻击：更改、伪装、重放、否认
		威胁可用性的攻击：拒绝服务（DoS）、分布式拒绝服务攻击（DDOS）
	解决方案：
		技术：加密和解密、服务（用于抵御攻击的服务，也即是为了上述安全目标特地设计的安全服务）
		加密和解密：
			传统加密方法：替代加密放法、置换加密方法
			现代加密方法：现代块加密方法
		服务：
			认证机制
			访问控制机制
		密钥算法和协议
			对称加密
			公钥加密
			单向加密
			认证协议
	Linux系统：OpenSSL（ssl），GPG（pgp）
		OpenSSL由三部分组成：
			ibencrpt库
			libssl库
			openssll多用途命令行工具
		加密算法和协议：
			对称加密：加密和解密使用同一个密钥
				DES：Data Encryption Standard
				3DES：Triple DES；
				AES：Advanced Encryption Standard；（128,192,256,384bits）
				Blowfish
				Twofish
				RC6
				CAST5
				特性：
					1、加密、解密使用同一个密钥；
					2、将原始数据分割为固定大小的块，逐个进行加密；
				缺陷：
					1、密钥过多
					2、密钥分发困难；
			公钥加密：密钥分为公钥与私钥，
				公钥：从私钥中提取产生；可公开给所有人；pubkey
				私钥：通过工具创建，使用者自己保存，必须保证其私密性；secret key；
				特点：用公钥加密，只能使用与之配对的私钥解密，反之亦然；
				用途：
					数字签名：主要在于让接收方确认发送方的身份；
					密钥交换：发送方用对方公钥加密一个对称密钥，并发送给对方
					数据加密：
				算法：RSA，ESA，ELGamal
					DSS：Dogotal Signature Algorithm
			单向加密：即提出数据指纹；只能加密，不能解密
				特征：定长输出，雪崩效应；
				功能：完整性
				算法：
					md5：Message Digest5,128bits
					sha1：Secure Hash Algorithm 1，160bits
						sha224，sha256，sha384，sha512
			密钥交换：IKE（Internet Key Exchange）
				公钥加密
				DH（Deffie-Hellman）
					A：p，g
					B：p，g
					A：x
						--> p^g%g==>B
						A：(p^y%g)^x=p^yx%g
					B：y
						--> p^y%g==>
						B：(p^x%g)^y=p^xy%g
		PKI：Public key Infrastructure
			公钥基础设施：
				鉴证机构：CA
				注册机构：RA
				证书吊销机构：CRL
				证书存取库：
			X.509：定义了证书的结构以及证书协议标准：
				版本号
				序列号
				签名算法ID
				发行者名称
				有效期限
				主体名称
				主体公钥
				发行者的惟一标识
				主体的惟一标识
				扩展
				发行者的签名
		SSL：Secure Sockets Layer
			Netscape：1994
			V1.0，V2.0，V3.0
		TLS：Transport Layer Security
			IETF：1999
			V1.0，V1.2，V1.3
			分层设计：
				1、最底层：基于算法原语的实现：aes，rsa，md5
				2、向上一层：各种算法的实现；
				3、在向上一层：组合算法实现的半成品；
				4、用各种组件拼装而成的各种成品密码学协议软件；
				协议的开源实现：OpenSSL
			OpenSSL：
				libcrypto库
				libssl库
				OpenSSL
			SSL会话主要三步：
				客户端向服务器端索要并验证证书
				双方协商生成“会话密钥”；
				双方采用“会话密钥”进行加密信息；
				SSL Handshare Protocol：
					第一阶段：clientHello：
						支持的协议版本，比如1.2
						客户端生成一个随机数，稍后用户生成“会话密钥”
						支持的加密算法，比如：AES，RSA；
						支持的压缩算法
					第二阶段：ServerHello
						确认使用的加密通信协议版本，比如tls 1.2；
						服务器端生成一个随机数，稍后用于生成“会话密钥”
						确认使用的加密方法；
						服务器证书；
					第三阶段：
						验证服务器证书；（发证机构、证书完整性、证书持有者、证书有效期、吊销列表）
						发送以下信息给服务器端：
							一个随机数；
							编码变更通知；表示随后的信息都用到双方协商的加密方法和密钥发送；
							客户端握手结束通知；
					第四阶段：
						收到客户端发来的第三个随机数pre-master-key后，计算生成本次会话所用到的会话密钥；
						向客户端发送如下信息：
							编码变更通知，表示随后的细腻都将双方商定的加密方法和密钥发送；
							服务端握手和结束通知；
	PKI：公钥基础设施：
		签证机构：CA
		注册机构：RA
		证书吊销机构：CRL
		证书存取库
	OpenSSL工具：
		组件：
			libcrypto，libssl主要由ssl开发者使用
			openssl：多用途命令行工具
		命令分类：
			标准命令
			消息摘要命令（dgst）
			加密命令（enc）
			标准命令：enc，ca，req，genrsa
			对称加密：
				工具：openssl，gpg
				支持算法：3des，aes，blowfish，towfish
				enc命令：
					加密：openssl enc -e -des3 -a -salt -in 加密文件 -out 加密后的文件
					解密：openssl enc -d -des3 -a -salt -out 解密后的文件 -in 加密后的文件
			单向解密：
				工具：openssl dgst，md5sum，sha1sum，sha224sum，….
				dgst命令：
					openssl dgst -md5 /PATH/TO/SOMEFILE
			生成用户密码：
				工具：passwd，openssl passwd
					openssl passwd -1 -salt SALT
			生成随机数：
				工具：openssl rand
				openssl rand -hex NUM
				openssl rand -base64 NUM
			公钥加密：
				加密解密：
					算法：RSA，ELGamal
					工具：openssl rsault，gpg
				数字签名：
					算法：RSA，DSA，ELGamal
					工具：openssl rsault，gpg
				密钥交换：
					算法：DH
				生成密钥：
					(umask 077; openssl genrsa -out /PATH/TO/PRIVATE_KEY_FILE number)
				提取公钥：
					openssl rsa -in /PATH/TO/PRIVATE_KEY_FILE -pubout
		Linux系统上的随机数生成器：
			/dev/random：仅从熵池返回随数；随机数用尽，阻塞；
			/dev/urandom：从熵池返回随机数；随机数用尽，会利用软件生成伪随机数，非阻塞；
				伪随机数不安全；
				熵随机数的来源：
					硬盘IO中断时间间隔；
					硬盘IO中断时间间隔；
		CA：
			公共信任的CA，私有CA；
			建立私有CA：
				openssl
				openCA
			openssl命令：
				配置文件：/etc/pki/tls/openssl.cnf 
				构建私有CA：
					在确定配置为CA的服务器上生成一个自签证书，并为CA提供所需要的目录及文件即可；
					步骤：
						（1）生成一个私钥：
							(umask 077;openssl genrsa -out /etc/pki/CA/private/cakey.pem 4096)
						（2）生成自签证书；
							openssl req -new -x509 /etc/pki/CA/private/cakey.pem -out /etc/pki/CA/cacert.pem -days 3655
								-new：生成新证书签署请求；
								-x509：生成自签格式证书，专用创建私有CA时；
								-key：生成请求时用到的私有文件路径；
								-out：生成的请求文件路径；如果自签操作将直接生成签署过的证书；
								-days：证书的有效时长，单元是day；
						（3）为CA提供所需的目录及文件：
							mkdir -pv /etc/pki/CA/{certs,crl,newcerts}
							touch /etc/pki/{serial,index.txt}
							echo 01 > /etc/pki/CA/serial
				要用到的证书进行安全通信的服务器，需要向CA请求签署证书：
					步骤：（以httpd为例）
						（1）用到的证书的主机生成证书签署请求；
							mkdir /etc/httpd/ssl
							cd etc/httpd/ssl
							(umask 077;openssl genrsa -out /etc/httpd/ssl/httpd.key 2048)
						（2）生成证书签署请求
							openssl req -new -key /etc/httpd/ssl/httpd.key -out /etc/httpd/ssl/httpd.csr -days 365
						（3）将请求通过可靠方式发给CA主机；
						（4）在CA主机签署证书；
							openssl ca -in /PATH/TO/CSR_FILE -out /etc/pki/CA/certs/httpd.crt -days 365
						（5）查看证书信息：
							openssl x509 -in /etc/pki/CA/certs/httpd.crt --noout -serial -subject
				吊销证书：
					步骤：
						（1）客户端获取要吊销的证书的serial（使用证书的主机）：
							openssl x509 -in /etc/pki/CA/certs/httpd.crt --noout -serial -subject
						（2）CA主机吊销证书
							先根据客户端提交的serial和subject信息，对比与本机数据库index.txt中存储的是否一致；
							吊销：
								openssl ca -revoke /etc/pki/CA/newcerts/SERIAL.pem
									其中的SERIAL要换为证书真正的序列号；
						（3）生成吊销证书的吊销编号（第一次吊销证书时执行）
							echo 01 > /etc/pki/CA/crlnumber
						（4）更新证书吊销列表
							openssl ca -genrsa -out thisca.crl
							查看crl文件：
								openssl crl -in /PATH/TO/CRL_FILE.crl -noout -text
博客作业：加密解密技术基础、PKI及创建私有CA；

