## Kafka Cluster Setup
Kafka Version: Kafka_2.12-2.3.0
(https://www.apache.org/dyn/closer.cgi?path=/kafka/2.3.0/kafka_2.12-2.3.0.tgz)

##### Component 

- 3 machine with OS RedHat 7
Minimum spec(per node):
Cpu – 2 cores
Memory - 4 GB
Data disk (/data) – 50 GB
System Diagram
- elasticsearch-7.8.0-linux-x86_64.tar.gz 
- JDK11 (8 or later)
​ 

### 1.) Prepare User 
- Run as root. 

##### 1.1) Config hosts 

```shell 
 sudo vi /etc/hosts
 <ip1> <hostname1>
 <ip2> <hostname2>
 <ip3> <hostname3>
 ```
 
##### 1.2) Create group and user
- ubuntu

```shell 
addgroup kafka
useradd kafka -u 3041 -s /bin/bash -m -d /home/kafka -g kafka
echo -e "kafka\nkafka" | passwd kafka
chage -I -1 -m 0 -M 99999 -E -1 kafka
```
 
- redhat

```shell 
groupadd kafka
useradd kafka -u 3041 -s /bin/bash -m -d /home/kafka -g kafka
echo -e "kafka\nkafka" | passwd kafka
chage -I -1 -m 0 -M 99999 -E -1 kafka
```

​ 
### 2.) Install Java and Download Kafka
- Run as root. 

##### 1.2) Install Java

- ubuntu
```shell 
sudo apt-get install openjdk-11-jdk
```

- redhat
```shell
yum install java-11-openjdk
```

##### 1.3) Download binary Kafka file
- Run as root.

- ubuntu
```shell
cd /data
wget https://www-eu.apache.org/dist/kafka/2.3.0/kafka_2.12-2.3.0.tgz
```

- redhat
```shell
cd /data
yum install wget
yum install nmap-ncat
wget https://www-eu.apache.org/dist/kafka/2.3.0/kafka_2.12-2.3.0.tgz
```

​ 
### 3.) Install Kafka
- Run as root. 

##### 3.1) Create working path 
```shell
tar -xf kafka_2.12-2.3.0.tgz
mkdir -p /data/kafkadata/kafka-bin
mkdir /data/kafkadata/zookeeper
mkdir /data/kafkadata/kafka-logs
cd /data/kafka_2.12-2.3.0
mv *  /data/kafkadata/kafka-bin
cd /data
rm -rf kafka_2.12-2.3.0
chown -R kafka:kafka kafkadata
```

##### 3.2) Update config
- Run as kafka.

```shell
cd /data/kafkadata/kafka-bin/config
vi zookeeper.properties

  # change zookeeper working path
  dataDir=/data/kafkadata/zookeeper
  admin.enableServer=true
  # add configuration for zookeeper cluster 
  initLimit=5
  syncLimit=2
  server.0=<hostname1>:2888:3888
  server.1=<hostname2>:2888:3888
  server.2=<hostname3>:2888:3888
 
vi server.properties
 
  #update kafka configuration
  Broker.id=0 or 1 or 2
  listeners = PLAINTEXT://<hostname>:9092
  log.dirs=/data/kafkadata/kafka-logs
  num.partitions=3
  log.retention.hours=168
  offsets.topic.replication.factor=3
  transaction.state.log.replication.factor=3
  transaction.state.log.min.isr=3
  zookeeper.connect=<hostname1>:2181,<hostname2>:2181,<hostname3>:2181
```
##### 3.3) Create myid
- Run as kafka.

```shell
cd /data/kafkadata/zookeeper
vi myid
 
  0 or 1 or 2
```

##### 3.4) Export Kafka path
- Run as kafka.

```shell
cd /home/kafka/
vi .bashrc
 
  export KAFKA_HOME=/data/kafkadata/kafka-bin
  export PATH=$PATH:$KAFKA_HOME/bin
 
source .bashrc
```

##### 3.5) Start Zookeeper
- Run as kafka.

```shell
zookeeper-server-start.sh -daemon /data/kafkadata/kafka-bin/config/zookeeper.properties
```

##### 3.6) Start Kafka Server
- Run as kafka.

```shell
kafka-server-start.sh -daemon /data/kafkadata/kafka-bin/config/server.properties
 ```
 
 ​ 
 ### 4.) Varify Kafka Node
- Run as kafka.

##### 4.1) Check on each node

- Check leader node
```
echo stat |nc <hostname1> 2181|grep Mode
echo stat |nc <hostname2> 2181|grep Mode
echo stat |nc <hostname3> 2181|grep Mode
```
 
- On zookeeper leader node
```
echo mntr | nc <leader node> 2181|grep follower
```
- Must have 2 zk_followers and 2 zk_synced_followers

