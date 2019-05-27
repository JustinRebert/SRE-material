1.安装gitlab

```bash
yum install -y vim gcc gcc-c++ wget net-tools lrzsz iotop lsof iotop bash-completion curl policycoreutils openssh-server openssh-clients postfix
yum install -y gitlab-ce-11.9.8-ce.0.el7.x86_64.rpm
```

gitlab配置

```bash
grep "^[a-Z]" /etc/gitlab/gitlab.rb 
external_url 'http://172.20.102.34'
gitlab_rails['gitlab_email_from'] = '1009610436@qq.com'
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "1009610436@qq.com"
gitlab_rails['smtp_port'] = 25
gitlab_rails['smtp_user_name'] = "Asone"
gitlab_rails['smtp_password'] = "123456"
gitlab_rails['smtp_domain'] = "qq.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_tls'] = false
user['git_user_email'] = "1009610436@qq.com"
```

配置及其初始化

```
 gitlab-ctl reconfigure
```

启动验证

```bash
gitlab-ctl start
```

安装java

```bash
yum install -y glibcc.i686
tar xf /usr/local/src/jdk-8u211-linux-i586.tar.gz -C /usr/local/src/
ln -s /usr/local/src/jdk1.8.0_211 /usr/local/java
[root@localhost ~]# vim /etc/profile.d/java.sh 
export JAVA_HOME=/usr/local/java 
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH 
export CLASSPATH=.$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib:$JAVA_HOME/lib/tools.jar
source /etc/profile.d/java.sh
```

安装tomcat

```bash
tar -xf  apache-tomcat-8.5.40.tar.gz -C /usr/local/
ln -sv /usr/local/apache-tomcat-8.5.40 /usr/local/tomcat
chown -R tomcat.tomcat /usr/local/tomcat/
vim /etc/profile.d/tomcat.sh
export CATALINA_HOME=/usr/local/tomcat
export PATH=$CATALINA_HOME/bin:$PATH
export CLASSPATH=.$CLASSPATH=$CATALINA_HOME/lib
source /etc/profile.d/tomcat.sh
```

登录
