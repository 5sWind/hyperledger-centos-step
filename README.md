# hyperledger-centos-step

## Ansible
```
sudo yum install epel-release
sudo yum install ansible
```
## 配置
```
sudo vi /etc/ansible/hosts
```
```
[k8s:children]
worker1
worker2

[worker1]
ecs-f4e0-0001 ansible_host=192.168.0.26 ansible_port=22 ansible_user=root

[worker2]
ecs-f4e0-0002 ansible_host=192.168.0.216 ansible_port=22 ansible_user=root
```
## 生成pub key
```
ssh-keygen
ssh-copy-id -i ~/.ssh/id_rsa root@192.168.0.216
ssh-copy-id -i ~/.ssh/id_rsa root@192.168.0.26
ansible -m ping all
```

## run playbook to install fabric
```
git clone https://github.com/5sWind/hyperledger-centos-step.git
cd hyperledger-centos-step/ansible && ansible-playbook install-hyperledger.yml
```

## install fabric on master node
```
cd /home
git clone https://github.com/WhiteMatrixTech/fabric-external-chaincodes.git
cd fabric-external-chaincodes
chmod 744 ./fabricOps.sh
./fabricOps.sh start
```

## copy files to worker nodes
```
cd hyperledger-centos-step/ansible && ansible-playbook fabric-bootstrap.yml
```

## bootstrap hyperledger fabric on master node
```
kubectl create ns hyperledger
cd /home/fabric-external-chaincodes && kubectl create -f orderer-service/ && kubectl create -f org1/ && kubectl create -f org2/ && kubectl create -f ingress/ && kubectl get pods -n hyperledger
```

## clean up
```
kubectl delete ns hyperledger

# this is to remove orderer and peer related files
# on worker node.
rm -rf /home/storage
```

## 容器操作
### 进入容器
```
kubectl exec -it cli_org1_pod_name -n hyperledger -- sh
```

### 创建channel
```
peer channel create -o orderer0:7050 -c mychannel -f ./scripts/channel-artifacts/channel.tx --tls true --cafile $ORDERER_CA
```

### 加入channel
```
peer channel join -b mychannel.block
```

### 进入另一容器
```
kubectl exec -it cli_org2_pod_name -n hyperledger -- sh
```

### fetch channel
```
peer channel fetch 0 mychannel.block -c mychannel -o orderer0:7050 --tls --cafile $ORDERER_CA
```

### 加入
```
peer channel join -b mychannel.block
```

### 获取channel list
```
peer channel list
```
