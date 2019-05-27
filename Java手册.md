1.下载软件：

```bash
[root@host-172-20-102-31 ~]# yum install glibc.i686 -y
wget https://download.oracle.com/otn-pub/java/jdk/11.0.2+9/f51449fcd52f4d52b93a989c5c56ed3c/jdk-11.0.2_linux-x64_bin.tar.gz?AuthParam=1555419979_cb82d0a36cf716697fc408be97bf0187 -O jdk-11.0.2_linux-x64_bin.tar.gz
```

2.解压

```
cd /usr/local/src/
tar xf jdk-11.0.2_linux-x64_bin.tar.gz
```

3.设置环境变量

```bash
ln -sv /usr/local/src/jdk-11.0.2/ /usr/local/java
```

```bash
vim /etc/profile.d/java.sh
export JAVA_HOME=/usr/local/java
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar$JAVA_HOME/lib/tools.jar
```

```bash
source /etc/profile.d/java.sh
java --version
javac --version
```

