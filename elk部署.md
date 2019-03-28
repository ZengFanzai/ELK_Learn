# ELK环境部署

## 服务器环境: debian 9

### 安装jre环境

#### (Oracle Jre)
```sh
wget https://download.oracle.com/otn-pub/java/jdk/8u192-b12/750e1c8617c5452694857ad95c3ee230/server-jre-8u192-linux-x64.tar.gz?AuthParam=xxxxxx(根据需求修改,从html源码获取)
```

#### (openJDK)

```sh
sudo apt update
sudo apt install default-jre
```

### 安装ES(6.4.3)

```sh
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.4.3.deb
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.4.3.deb.sha512
shasum -a 512 -c elasticsearch-6.4.3.deb.sha512
sudo dpkg -i elasticsearch-6.4.3.deb
```

### 查看系统使用的是init还是systemd

```sh
ps -p 1
```

### 将ES添加到service并设置开机启动

- **init**

```sh
sudo update-rc.d elasticsearch defaults 95 10 #开机自启
sudo -i service elasticsearch start #开启
sudo -i service elasticsearch stop  #关闭
```

- **systemd**

**参考**:[Systemd 入门教程：命令篇](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)

```sh
sudo /bin/systemctl daemon-reload #重载所有修改过的配置文件
sudo /bin/systemctl enable elasticsearch.service
sudo systemctl start elasticsearch.service #启动
sudo systemctl stop elasticsearch.service #关闭
```

[更多详情](https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html)

### 安装ik分词器(下载的是zip格式(自选))

* 在ES的plugins文件夹中新建ik文件夹并解压zip将文件复制到这（6.4.3）

```sh
wget https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.4.3/elasticsearch-analysis-ik-6.4.3.zip
```

### 安装logstash(6.4.3)

```sh
wget https://artifacts.elastic.co/downloads/logstash/logstash-6.4.3.deb
sudo dpkg -i logstash-6.4.3.deb
```

#### 将logstash添加到service并设置开机启动

- **init**

```sh
sudo update-rc.d logstash defaults 95 10 #开机自启
sudo -i service logstash start #开启
sudo -i service logstash stop #关闭
```

- **systemd**

```sh
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable logstash.service
sudo systemctl start logstash.service #启动
sudo systemctl stop logstash.service #关闭
```

#### 我需要通过从mysql查询数据，然后通过logstash同步到ES中

- 添加mysql的java-connector(5.1.47) 启动之前检测一下是否文件为空（我遇到了解压的jar为0字节）

```sh
wget https://cdn.mysql.com//Downloads/Connector-J/mysql-connector-java-5.1.47.zip
```

- 编写配置文件并重启logstash服务

- zip: https://artifacts.elastic.co/downloads/logstash/logstash-6.4.3.zip

### 安装Kibana

```sh
wget https://artifacts.elastic.co/downloads/kibana/kibana-6.4.3-amd64.deb
shasum -a 512 kibana-6.4.3-amd64.deb
sudo dpkg -i kibana-6.4.3-amd64.deb
```

- **init**

```sh
sudo update-rc.d kibana defaults 95 10 #开机自启
sudo -i service kibana start #开启
sudo -i service kibana stop #关闭
```

- **systemd**

```sh
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable kibana.service
sudo systemctl start kibana.service #启动
sudo systemctl stop kibana.service #关闭
```

### 设置ip策略---针对ES的9200端口和Kibana的5601端口

```sh
iptables -I INPUT -p tcp --dport 9200 -j DROP #拒绝所有ip访问9200端口
iptables -I INPUT -p tcp --dport 5601 -j DROP #拒绝所有ip访问5601端口
iptables -I INPUT -s xxx.xxx.xxx.xxx -p tcp --dport 9200 -j ACCEPT #开放特定ip访问9200
iptables -I INPUT -s xxx.xxx.xxx.xxx -p tcp --dport 5601 -j ACCEPT #开放特定ip访问5601
```

#### **提示:**

- 将iptables配置保存到/etc/my_iptables，这个文件名可以自己定义，与下面的配置一致即可

```sh
iptables-save > /etc/my_iptables
```

- 同时可设定服务器启动自动加载ip策略

```sh
iptables-restore < /etc/my_iptables
```