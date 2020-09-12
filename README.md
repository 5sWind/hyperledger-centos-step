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
[k8s]
ecs-f4e0-0002 ansible_ssh_host=192.168.0.216 ansible_user=root
ecs-f4e0-0001 ansible_ssh_host=192.168.0.26 ansible_user=root
```
## 生成pub key
```
ssh-keygen
ssh-copy-id -i ~/.ssh/id_rsa root@192.168.0.216
ssh-copy-id -i ~/.ssh/id_rsa root@192.168.0.26
ansible -m ping all
```

## run playbook
```
git clone https://github.com/5sWind/hyperledger-centos-step.git
cd hyperledger-centos-step/ansible && ansible-playbook install-hyperledger.yml
```

## bootstrap hyperledger fabric on master node
```
kubectl create ns hyperledger
cd /home/fabric-external-chaincodes && kubectl create -f orderer-service/ && kubectl create -f org1/ && kubectl create -f org2/ && kubectl get pods -n hyperledger
```

## 进入容器
```
kubectl exec -it cli-org1-cd94d9b97-69bpw -n hyperledger -- sh
```
