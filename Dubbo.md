一、安装部署前的准备工作：

1、关闭SELinux和firewalld

2、服务器时间校对

3、安装配置JDK：

\# rpm -ivh jdk-8u192-linux-x64.rpm

\# vim /etc/profile.d/jdk.sh

export JAVA_HOME=/usr/java/latest

export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

export PATH=$JAVA_HOME/bin:$PATH

\# . /etc/profile.d/jdk.sh

\# java -version

![image.png](https://s1.51cto.com/images/20181217/1545029230266361.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

4、安装配置Maven（http://maven.apache.org/download.cgi）：

Maven是基于项目对象模型（POM）的项目管理及自动构建工具。

\# tar -xf apache-maven-3.6.0-bin.tar.gz -C /usr/local

\# cd /usr/local

\# ln -sv apache-maven-3.6.0 maven

\# vim /etc/profile.d/maven.sh

export MAVEN_HOME=/usr/local/maven

export PATH=$MAVEN_HOME/bin:$PATH

\# . /etc/profile.d/maven.sh

\# mvn -v

![image.png](https://s1.51cto.com/images/20181217/1545029246582474.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

创建本地资源仓库：# mkdir -pv /data/maven/local-Repository   //不创建的话默认为：${user.home}/.m2/repository

\# cd /usr/local/maven/conf

\# cp settings.xml settings.xml.bak

\# vim settings.xml

（1）修改本地仓库位置：<localRepository>/data/maven/local-Repository</localRepository>

![image.png](https://s1.51cto.com/images/20181217/1545029261854483.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

（2）在<mirrors></mirrors>配置段中新增如下代码：

<mirror>

<id>alimaven</id>

<mirrorOf>central</mirrorOf>

<name>aliyun maven</name>

<url>http://maven.aliyun.com/nexus/content/groups/public/</url>

</mirror>

![image.png](https://s1.51cto.com/images/20181217/1545029286515768.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)



二、安装部署ZooKeeper（http://mirrors.hust.edu.cn/apache/zookeeper/）：

ZooKeeper是一款开源的、分布式应用程序协调服务，是Google的Chubby一个开源实现，是Hadoop和Hbase的重要组件。它是一个为分布式应用提供一致性服务的软件，提供的功能包括：配置维护、域名服务、分布式同步、组服务等。ZooKeeper的目标就是封装好复杂易出错的关键服务，将简单易用的接口和性能高效、功能稳定的系统提供给用户。

1、安装ZooKeeper：

\# tar -xf zookeeper-3.4.13.tar.gz -C /usr/local

\# cd /usr/local

\# ln -sv zookeeper-3.4.13 zookeeper

2、配置ZooKeeper：

\# vim /etc/profile.d/zookeeper.sh

export ZOOKEEPER_HOME=/usr/local/zookeeper

export PATH=$ZOOKEEPER_HOME/bin:$PATH

\# . /etc/profile.d/zookeeper.sh

创建日志目录和数据目录：

\# cd /usr/local/zookeeper

\# mkdir -pv {logs,data}

\# cd conf

\# cp zoo_sample.cfg zoo.cfg

\# vim zoo.cfg，修改或新增如下代码：

dataDir=/usr/local/zookeeper/data

dataLogDir=/usr/local/zookeeper/logs

3、启动ZooKeeper：

\# zkServer.sh start

![image.png](https://s1.51cto.com/images/20181217/1545029320155749.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

\# ss -tunlp | grep 2181

\# ps -ef | grep java

\# jps

![image.png](https://s1.51cto.com/images/20181217/1545029328798144.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

备注：QuorumPeerMain进程代表当前节点ZooKeeper已经启动成功

4、查看ZooKeeper状态：

\# zkServer.sh status

![image.png](https://s1.51cto.com/images/20181217/1545029358870349.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

\# find / -name zookeeper.out

![image.png](https://s1.51cto.com/images/20181217/1545029365765283.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

\# tail /root/zookeeper.out

![image.png](https://s1.51cto.com/images/20181217/1545029379439483.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)



三、安装部署Dubbo-admin：

Dubbo是阿里巴巴开源的一款高性能、轻量级的Java RPC框架，它提供了三大核心能力：面向接口的远程方法调用、智能容错和负载均衡以及服务自动注册和发现，使得应用可通过高性能的RPC实现服务的输出和输入功能，可以和Spring框架无缝集成。

1、演示版本说明：https://github.com/apache/incubator-dubbo-ops，默认为develop分支

![image.png](https://s1.51cto.com/images/20181217/1545029400957860.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

新版本的Dubbo将dubbo-admin分为了前端和后端，前端需要安装Vue.js的开发环境Node.js，以及其默认包管理器工具npm，为了方便演示，此次使用的是master分支：

![image.png](https://s1.51cto.com/images/20181217/1545029419742562.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

2、安装部署dubbo-admin：

\# yum -y install git

\# cd /usr/local

\# git clone -b master https://github.com/apache/incubator-dubbo-ops.git

![image.png](https://s1.51cto.com/images/20181217/1545029427550807.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

\# cd incubator-dubbo-ops

\# mvn package

![image.png](https://s1.51cto.com/images/20181217/1545029435818467.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

备注：Maven常用命令介绍

Ø  mvn clean：清理项目打包文件，即项目下的target目录

Ø  mvn compile：编译项目下的src/main/Java目录源代码

Ø  mvn deploy：项目打包并发布到远程仓库

Ø  mvn install：项目打包并发布到本地仓库

Ø  mvn package：项目打包，即在项目target目录下生成编译后的jar或war等文件

Ø  mvn test：单元测试命令，执行src/test/java目录下的junit单元测试用例

项目打包用时较长：

![image.png](https://s1.51cto.com/images/20181217/1545029460644070.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

\# cd dubbo-admin/target

\# vim classes/application.properties

![image.png](https://s1.51cto.com/images/20181217/1545029468125652.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

\# nohup java -jar dubbo-admin-0.0.1-SNAPSHOT.jar &

\# ps -ef | grep dubbo-admin | grep -v grep

![image.png](https://s1.51cto.com/images/20181217/1545029475338386.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

\# ss -tunlp | grep 7001

\# ls /data/maven/local-Repository

![image.png](https://s1.51cto.com/images/20181217/1545029486371033.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

3、浏览器中输入**http://192.168.1.148:7001，用户名和密码均为root*

![image.png](https://s1.51cto.com/images/20181217/1545029502186494.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

![image.png](https://s1.51cto.com/images/20181217/1545029506219777.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

![image.png](https://s1.51cto.com/images/20181217/1545029511809921.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)



四、安装部署Dubbo-monitor：

1、安装部署dubbo-monitor：

\# cd /usr/local/incubator-dubbo-ops/dubbo-monitor-simple/target

\# tar -xf dubbo-monitor-simple-2.0.0-assembly.tar.gz

\# cd dubbo-monitor-simple-2.0.0

\# vim conf/dubbo.properties

![image.png](https://s1.51cto.com/images/20181217/1545029532731798.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

2、启动dubbo-moniotr：

\# assembly.bin/start.sh

![image.png](https://s1.51cto.com/images/20181217/1545029542593754.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
# ps -ef | grep dubbo-monitor
# ss -tunlp | grep 8080

# ls /root/monitor
```

![image.png](https://s1.51cto.com/images/20181217/1545029548495185.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
# ls /root/monitor/charts
```

![image.png](https://s1.51cto.com/images/20181217/1545029554181032.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```
# ls /root/monitor/statistics
```

![image.png](https://s1.51cto.com/images/20181217/1545029563962406.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

3、浏览器中输入http://192.168.1.148:8080

![image.png](https://s1.51cto.com/images/20181217/1545029577343249.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

![image.png](https://s1.51cto.com/images/20181217/1545029581137379.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)





![1556803171739](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1556803171739.png)

![1556803193947](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1556803193947.png)