## Deploy Zookeeper/Kafka with Ansible
**This repo is deploy Zookeeper/Kafka with Ansible**

Default Version

|Name|Version| 
|:---:|:---:|
|Zookeeper|3.4.11|
|Kafka|2.12-1.0.0|

Above information setting in repo file `roles/download/templates/kafka_packages.yml.j2`

Do it
------
1. First of all
2. Modify inventory.ini file
3. Prepare
4. Deploy
5. Start
6. Stop
7. Manual start/stop
8. Test
9. Expansion


### First of all
- Install ansible
	- deb `apt-get install ansible -y`
	- rpm `yum install ansible -y`
- Clone repo
	- `git clone https://github.com/jomenxiao/kafka-ansible.git`
- Change directoy
	- `cd kafka-ansible`

### Modify inventory.ini file
- Zookeeper configure informations
	- `myid` is unique integer,range 1-255; introduce documents:[URL](http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_configuration)
	- `deploy_dir` deploy directory
	- one line one process
	- example: `zk_1 ansible_host=172.17.8.201  deploy_dir=/home/tidb/zk_deploy myid=1`

- Kafka configure informations
	- `kafka_port` for client connect 
	- `id` is the kafka's broker. This must be set to a unique integer for each broker.
	- one line one process
	- example: `kafka1_1 ansible_host=172.17.8.201 deploy_dir=/home/tidb/kafka_deploy1 kafka_port=9091  id=1`
	
### prepare 
- Localhost create some directory for deploy 
- Localhost create facts for deploy
- Localhost create deploy directory 
- Localhost Modify kernel params
- Localhost download zookeeper/kafka
- Remote host install necessary packages
	- setting in repo directory `roles/packages/packagesfiles`
	- example: java package

`ansible-playbook -i inventory.ini prepare.yml`

### deploy
- Deploy packages to remote host
- Modify configure file 
- generate start/stop scripts

`ansible-playbook -i inventory.ini deploy.yml`

### Start
- Start zookeeper first
- Start kafka
 
`ansible-playbook -i inventory.ini start.yml`

### Stop
- stop kafka first
- stop zookeeper
 
`ansible-playbook -i inventory.ini stop.yml`

### Manual start/stop
- Zookeeper
	- `cd $deploy_dir/scripts && ./run_zookeeper.sh start|status|stop"`
- Kafka
	- `cd $deploy_dir/scripts && ./run_kafka.sh start|stop"`
	
### Test
- `tools` directory
	- start consumer
	`tools/kafka-console-consumer -brokers="172.17.8.201:9091,172.17.8.201:9092,172.17.8.202:9091,172.17.8.202:9092,172.17.8.203:9091,172.17.8.203:9092" -topic=test`
	- start producer
		`tools/kafka-console-producer -brokers="172.17.8.201:9091,172.17.8.201:9092,172.17.8.202:9091,172.17.8.202:9092,172.17.8.203:9091,172.17.8.203:9092" -topic=test -value=world -key=hello`
		
### Expansion
- Add host to inventory.ini file
- `ansible-playbook -i inventory.ini prepare.yml --diff`
- `ansible-playbook -i inventory.ini deploy.yml --diff`
- `ansible-playbook -i inventory.ini start.yml --diff`

Deploy directory structure
------
### Zookeeper
```
zk_deploy/
├── backup
├── data
│   ├── myid
│   ├── version-2
│   └── zookeeper_server.pid
├── datalog
│   └── version-2
├── log
│   └── zookeeper.out
├── package
│   └── zookeeper-3.4.11
├── scripts
│   └── run_zookeeper.sh
└── zk -> /home/tidb/zk_deploy/package/zookeeper-3.4.11
```

### Kafka
```
kafka_deploy1/
├── backup
├── datalog
│   ├── cleaner-offset-checkpoint
│   ├── __consumer_offsets-2
│   ├── log-start-offset-checkpoint
│   ├── meta.properties
│   ├── recovery-point-offset-checkpoint
│   ├── replication-offset-checkpoint
│   └── test-1
├── kafka -> /home/tidb/kafka_deploy1/package/kafka_2.12-1.0.0
├── log
│   ├── controller.log
│   ├── kafka-authorizer.log
│   ├── kafka-request.log
│   ├── kafkaServer-gc.log.0.current
│   ├── kafkaServer.out
│   ├── log-cleaner.log
│   ├── server.log
│   └── state-change.log
├── package
│   └── kafka_2.12-1.0.0
└── scripts
    └── run_kafka.sh
```

Attentions
------
- **restart kafka need 6 seconds interval**