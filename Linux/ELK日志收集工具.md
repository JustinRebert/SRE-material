环境：

172.20.102.34 elasticsearch+kibana

172.20.102.151 elasticsearch

172.20.102.98 logstach

172.20.102.167 logstach

ElasticSearch安装

必须安装有java环境，两台安装方法一样，只是节点名称不同

```bash
[root@host-172-20-102-34 ~]# cd /usr/local/src
[root@host-172-20-102-34 src]# yum install elasticsearch-5.3.0.rpm -y
[root@host-172-20-102-34 ~]# grep "^[a-Z]" /etc/elasticsearch/elasticsearch.yml 
cluster.name: my-application
node.name: node-1
path.data: /elk/data
path.logs: /elk/logs
bootstrap.memory_lock: true
network.host: 0.0.0.0
http.port: 9200
discovery.zen.ping.unicast.hosts: ["172.20.102.34", "172.20.102.151"]
[root@host-172-20-102-34 ~]# vim /usr/lib/systemd/system/elasticsearch.service
LimitMEMLOCK=infinity
```

![1556523807323](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1556523807323.png)

安装elasticsearch head插件，另外一台也是一样的配置

```bash
[root@host-172-20-102-34 ~]# yum install git npm -y
[root@host-172-20-102-34 src]# git clone https://github.com/mobz/elasticsearch-head.git
[root@host-172-20-102-34 src]# cd elasticsearch-head
[root@host-172-20-102-34 elasticsearch-head]# npm install grunt -y
[root@host-172-20-102-34 elasticsearch-head]# npm install
[root@host-172-20-102-34 ~]# npm run start&
[root@host-172-20-102-34 ~]# tail -2 /etc/elasticsearch/elasticsearch.yml
http.cors.enabled: true
http.cors.allow-origin: "*"
[root@host-172-20-102-34 ~]# systemctl restart elasticsearch.service
```

![1556525427866](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1556525427866.png)

集群健康报红色的

![1557057731596](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1557057731596.png)

elasticsearch集群监控状态

```bash
[root@host-172-20-102-241 ~]# curl –sXGET  http://172.20.102.34:9200/_cluster/health?pretty=true
```

安装kibana

```bash
[root@host-172-20-102-34 src]# yum install kibana-6.5.4-x86_64.rpm
[root@host-172-20-102-34 ~]# grep "^[a-Z]" /etc/kibana/kibana.yml
server.port: 5601
server.host: "0.0.0.0"
elasticsearch.url: "http://172.20.102.34:9200"
[root@host-172-20-102-34 ~]# systemctl status kibana.service
[root@host-172-20-102-34 ~]# systemctl enable kibana.service
```

![1556528374350](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1556528374350.png)

logstash安装，另外一台同样的配置

```
[root@host-172-20-102-98 src]# yum install logstash-6.5.4.rpm -y
[root@host-172-20-102-98 ~]# systemctl start logstash.service
```

写一个系统的配置文件，并重启

```
[root@host-172-20-102-98 ~]# cat /etc/logstash/conf.d/log-es.conf 
input {
  file {
    path => ["/var/log/messages"]
    type => "systemlog"
    start_position => "beginning"
  }
}

output {
	elasticsearch {
		hosts => ["172.20.102.34:9200"]
		index => "172.20.102.34-syslog-%{+YYYY.MM.dd}"
	}
}
```

特别注意：file { path => "FILE" }中的FILE必须要可读

如果需要删除数据，需要点击进去然后再删除

![1556545918929](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1556545918929.png)

多个日志收集配置

```bash
[root@host-172-20-102-98 ~]# vim /etc/logstash/conf.d/log-es.conf
input {
    file {
        path => ["/var/log/messages"]
        type => "systemlog"
        start_position => "beginning"
        stat_interval => "3"
    }
    file {
        path => "/var/log/secure"
        type => "securelog"
        start_position => "beginning"
        stat_interval => "3"
    }
}  

output {
    if [type] == "systemlog" {
        elasticsearch {
            hosts => ["172.20.102.34:9200"]
            index => "172.20.102.34-syslog-%{+YYYY.MM.dd}"
        }
    }
    if [type] == "securelog" {
        elasticsearch {
            hosts => ["172.20.102.34:9200"]
            index => "secury-log-%{+YYYY.MM.dd}"
        }
    }
}
```

![1556796604247](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1556796604247.png)

二进制安装，elk必须要用普通用户启动，否则会出错

![1557058807220](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1557058807220.png)

logstach搜集tomcat日志

```
[root@host-172-20-102-31 src]# tar xf apache-tomcat-8.5.40.tar.gz
[root@host-172-20-102-31 ~]# ln -sv /usr/local/src/apache-tomcat-8.5.40 /usr/local/tomcat
‘/usr/local/tomcat’ -> ‘/usr/local/src/apache-tomcat-8.5.40’
[root@host-172-20-102-31 ~]# mkdir /usr/local/tomcat/webapps/webdir
```

编写tomcat的server.xml

```
[root@host-172-20-102-31 ~]# vim /usr/local/tomcat/conf/server.xml
<Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs" prefix="tomcat_access_log" suffix=".log" pattern="{&quot;clientip&quot;:&quot;%h&quot;,&quot;ClientUser&quot;:&quot;%l&quot;,&quot;authenticated&quot;:&quot;%u&quot;,&quot;AccessTime&quot;:&quot;%t&quot;,&quot;method&quot;:&quot;%r&quot;,&quot;status&quot;:&quot;%s&quot;,&quot;SendBytes&quot;:&quot;%b&quot;,&quot;Query?string&quot;:&quot;%q&quot;,&quot;partner&quot;:&quot;%{Referer}i&quot;,&quot;AgentVersion&quot;:&quot;%{User-Agent}i&quot;}"/>
```

查看tomcat的访问日志

![1557060212191](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1557060212191.png)

测试json日志：www.kjson.com

![1557060539691](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1557060539691.png)

编写python脚本，自动获取ip

```
[root@host-172-20-102-31 ~]# vim ip.py
#!/usr/bin/env python
#coding:utf-8
#Author Zong Peng

data ={"clientip":"192.168.56.1","ClientUser":"-","authenticated":"-","AccessTime":"[20/May/2017:21:46:22 +0800]","method":"GET /webdir/ HTTP/1.1","status":"200","SendBytes":"12","Query?string":"","partner":"-","AgentVersion":"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36"}
ip=data["clientip"]
print ip
[root@host-172-20-102-31 ~]# python ip.py
```

编写tomcat的日志监控文件

```
[root@host-172-20-102-31 ~]# cat /etc/logstash/conf.d/tomcat.conf 
input {
  file {
    path => "/usr/local/tomcat/logs/tomcat_access_log.*.log"
    start_position => "end"
    type => "tomct-access-log"
  }
  file { 
    path => "/var/log/messages"
    start_position => "end"
    type => "system-log"
  }
}

output {
  if [type] == "tomct-access-log" {
    elasticsearch {
      hosts => ["172.20.102.33:9200"]
      index => "logstash-tomcat-5616-access-%{+YYYY.MM.dd}"
      codec => json
     }
   }

  if [type] == "system-log" {
    elasticsearch {
      hosts => ["172.20.102.10:9200"]
      index => "system-log-5616-%{+YYYY.MM.dd}"
     }
  }
}
```

![1557062874834](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1557062874834.png)

新添加一个elasticsearch服务

![1557103905301](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1557103905301.png)

收集java日志





grok插件

```
filter {
  if [type] == "apache" {
    grok {
      match => ["message" => "%{IPORHOST:addre} %{USER:ident} %{USER:auth} \[%{HTTPDATE:timestamp}\] \"%{WORD:http_method} %{NOTSPACE:request} HTTP/%{NUMBER:httpversion}\" %{NUMBER:status} (?:%{NUMBER:bytes}|-) \"(?:%{URI:http_referer}|-)\" \"%{GREEDYDATA:User_Agent}\""]
      remove_field => ["message"]
    }
    date {
      match => [ "timestamp", "dd/MMM/YYYY:HH:mm:ss Z" ]
    }
  }
}
```

自定义gork日志文件

```
filter {
  if [type] == "apache" {
    grok {
      patterns_dir => "/usr/local/logstash-2.3.4/ownpatterns/patterns"
      match => {
                "message" => "%{APACHE_LOG}"
                }
      remove_field => ["message"]
    }
    date {
      match => [ "timestamp", "dd/MMM/YYYY:HH:mm:ss Z" ]
    }
  }
}
```

