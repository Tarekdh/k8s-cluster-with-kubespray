# KUBESPRAY

* installation vagrant : cf Vagrantfile

* 1 machine deploiement, 1 master et 1 node

* user by default for each node `vagrant` pwd `vagrant`

* we are using ansible 2.9.20 and kubespray v2.15.1 
* we are using the box `ubuntu/bionic64`
 

<br>

* kubespray :
	* multi provider : on prem, openstack, gcp...
	* automatisation via ansible
	* idempotence
	* assertions
	* configuration de nombreux éléments
	* container runtime : docker vs cri
	* master/nodes/etcd splittés
	* choix pods réseaux : flannel / calico...
	* dashboard
	* ingress controller
	* certificats
	* attention cache (gather_facts si modifs après coup)

* utilisation du user vagrant (idéal utilisateur dédié avec clef ssh)

--------------------------------------------------------------------------------------

# Kubespray : Préparation


<br>

* sur la machine de déploiement : deploykub

* run these commands unsing root user to avoid issues `sudo su` 


`vagrant ssh deploykub`

<br>

* clone du dépôt (tag v2.15.1)

```
git clone -b v2.15.1 https://github.com/kubernetes-sigs/kubespray.git

cd /home/vagrant/kubespray
```

<br>

* installation de sshpass (ansible password)

```
sudo apt install sshpass
```

<br>

* installation des requirements

```
pip3 install -r requirements.txt
```

<br>

* on peut spécifier la conf du ansible.cfg
vim ansible.cfg

puis mettre ce code a la fin du fichier
```
[privilege_escalation]
become=True
become_method=sudo
become_user=root
become_ask_pass=False
```

---------------------------------------------------------------------------------------

# Kubespray : inventory ansible


<br>

* copier l'example dans votre dossier personnel (template kubespray)

```
cp -rfp inventory/sample inventory/my-cluster
```

* exemple : 1 master (avec etcd) et 1 nodes 
```
[all] # déclaration des nodes
node01 ansible_host=192.168.6.121  ip=192.168.6.121 etcd_member_name=etcd1
node02 ansible_host=192.168.6.122  ip=192.168.6.122
node05 ansible_host=192.168.6.125  ip=192.168.6.125
[kube-master]
node01
[etcd]
node01
[kube-node]
node05
node02
[calico-rr]
[k8s-cluster:children]
kube-master
kube-node
calico-rr
```

* run `export ANSIBLE_INVALID_TASK_ATTRIBUTE_FAILED=False`

ansible-playbook -i inventory/my-cluster/inventory.ini  --become --become-user=root -u vagrant -k -b cluster.yml

* connect to the master node `node01`

run `kubectl get nodes` to check that your work is done

Expected 

```
node01   Ready    control-plane,master   5h47m   v1.20.7
node05   Ready    <none>                 5h46m   v1.20.7
```

if you get this error 

```
The connection to the server localhost:8080 was refused - did you specify the right host or port?
```

you can run these commands to solve the problem

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

* get cluster info 

`kubectl cluster-info`

* deploy dashboard application

you can find instructions to deploy dashboard in link below

```
https://kubernetes.io/fr/docs/tasks/access-application-cluster/web-ui-dashboard/
```
* Recap

1- deploy
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/aio/deploy/recommended.yaml
```

2- change the service type to `NodePort`

you can use these commands to get service name

```
kubectl get svc -n kubernets-dashboard
kubectl edit svc service-name -n kubernets-dashboard
```

3- Run the proxy

```
kubectl proxy
```

4- create service to access to the application

instrucation are described in the link below
```
https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md
```


#install Helm

```
curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
sudo apt-get install apt-transport-https --yes
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```
