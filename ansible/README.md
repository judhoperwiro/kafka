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
