## deploy kafka with ansible
### modify inventory.ini file

### prepare 
`ansible-playbook -i inventory.ini prepare.yml`

### deploy
`ansible-playbook -i inventory.ini deploy.yml`

### start
`ansible-playbook -i inventory.ini start.yml`

## stop
`ansible-playbook -i inventory.ini stop.yml`