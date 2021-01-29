## Ansible Module for installation Kafka
​ 
#### Prerequisite

- ansible 2.9 
- vim

### 1.) Create Directory for Kafka 
```shell 
mkdir -p /data/ansible/kafka/source/
cd /data/ansible/kafka
vi install-kafka-2-5-0.yaml
vi inventory
```
### 2.) Run Ansible Playbook  
```shell 
ansible-playbook -i inventory install-kafka-2-5-0.yaml
```
### 3.) Limitiation  
```shell 
cannot start kafka using ansible, execute command manually

Start Zookeeper [user kafka]
$ zookeeper-server-start.sh -daemon /data/kafkadata/kafka-bin/config/zookeeper.properties
 
Start server [user kafka]
$ kafka-server-start.sh -daemon /data/kafkadata/kafka-bin/config/server.properties

