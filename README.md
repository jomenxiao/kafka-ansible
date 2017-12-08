## deploy kafka with ansible
### modify inventory.ini file
- zookeeper configure
	- `myid` is unique integer,range 1-255; introduce documents:[URL](http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_configuration)
	- `deploy_dir` deploy directory
	- example: `zk_1 ansible_host=172.17.8.201  deploy_dir=/home/tidb/zk_deploy myid=1`

- kafka configure
	- `kafka_port` for client connect 
	- `id` is the kafka's broker. This must be set to a unique integer for each broker.
	- example: `kafka1_1 ansible_host=172.17.8.201 deploy_dir=/home/tidb/kafka_deploy1 kafka_port=9091  id=1`
	
### prepare 
`ansible-playbook -i inventory.ini prepare.yml`

### deploy
`ansible-playbook -i inventory.ini deploy.yml`

### start
`ansible-playbook -i inventory.ini start.yml`

### stop
`ansible-playbook -i inventory.ini stop.yml`

## directory structure
### zookeeper
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

### kafka
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