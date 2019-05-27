# 操作系统的安装和常用配置

[TOC]



## 一、创建虚拟机及其硬件配置



安装好 VMware Workstation Pro之后创建虚拟机



（1）双击VMware Workstation Pro ——> 文件（File） ——> 新建虚拟机（New Virtual Machine）



![1553579548106](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553579548106.png)



（2）在弹出的窗口点击自定义配置，方便我们自己设置某些资源，并选择下一步



![1553579635926](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553579635926.png)



（3）在这里我以VMware Workstation15.X Pro  为例，创建虚拟机的硬件资源，并选择下一步执行



![1553579770726](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553579770726.png)



（4）在这里选择稍后安装操作系统，下一步继续虚拟机的各种资源



![1553579877993](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553579877993.png)



（5）选择操作系统类型为Linux，版本为CentOS7 64位，并继续下一步



![1553580024891](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553580024891.png)



（6）自定义虚拟机的名称并选择虚拟机放置在哪个目录下，选择好了就继续操作下去



![1553580374513](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553580374513.png)



（7）选择处理器数量和内核数量，并继续下一步操作



![1553580614606](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553580614606.png)



（8）自定义虚拟机的内存大小并继续下一步



![1553580748306](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553580748306.png)



（9）我在这里自定义网卡为NAT模式，并继续下一步



![1553580816365](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553580816365.png)



（10）默认I/O类型，并继续下一步



![1553580980901](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553580980901.png)



（11）选择默认的磁盘类型，并继续下一步



![1553581047259](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553581047259.png)



（11）创建新虚拟机磁盘，并继续下一步



![1553581134740](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553581134740.png)



（12）定义磁盘空间大小并选择存储为单个文件，继续下一步操作



![1553581368929](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553581368929.png)



（13）定义磁盘文件名称，并继续下一步操作



![1553581447923](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553581447923.png)



（14）完成虚拟机的创建。当然这里还可以自定义虚拟机的硬件设置



![1553581597347](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553581597347.png)



（15）创建好的虚拟机



![1553581783109](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553581783109.png)



## 二、Linux操作系统的安装



（1）启动客户机



![1553582075950](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553582075950.png)



（2）选择Install CentOS 7 并按下tab之后，输入net.ifnames=0 biosdevnames=0 selinux=0 selinux=0



![1553582621373](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553582621373.png)



（2）选择安装语言



![1553582838054](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553582838054.png)



（3）选择date与time



![1553582937247](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553582937247.png)





![1553583115262](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553583115262.png)



（4）选择自定义支持的语言



![1553583203852](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553583203852.png)



（5）选择磁盘分区和网卡配置



![1553583302791](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553583302791.png)





![1553583354414](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553583354414.png)





![1553583420115](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553583420115.png)





分区结果如图



![1553583492218](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553583492218.png)





![1553583541283](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553583541283.png)





![1553583707373](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553583707373.png)





（6）软件包



![1553583869440](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553583869440.png)



（7）最终的配置，并开始安装



![1553583759348](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553583759348.png)



（8）创建管理员密码并创建普通账户



![1553583952035](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553583952035.png)



（9）重启即开始使用Linux操作系统



![1553585041810](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553585041810.png)



## 三、安装后基本配置

```bash
vi /etc/ssh/sshd_conf
UseDNS no #UseDNS是否使用dns反向解析
GSSAPIAuthentication no #指定是否允许基于GSSAPI的用户认证，默认为no
mkdir -vp /etc/yum.repos.d/back
mv /etc/yum.repos.d/*.repo /etc/yum.repos.d/back
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
yum update -y && yum -y install net-tools vim bash-completion epel-release wget #更新并安装常用软件并安装epel源
grep -v "#" /etc/sysconfig/selinux | grep -v "^$"
SELINUX=disabled #
SELINUXTYPE=targeted #
systemctl stop firewalld.service #停止防火墙
systemctl disable firewalld.service #禁用防火墙，防止下次自动启动防火墙
reboot
```

