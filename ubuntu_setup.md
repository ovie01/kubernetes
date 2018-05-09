#Run update & Upgrade
```
sudo apt-get update 
sudo apt-get upgrade
```
#You should have these ports open
      MASTER
Kubernetes API server      TCP   6443*
etcd server client API	   TCP   2379-2380
Kubelet API                TCP   10250
Kube-scheduler             TCP   10251
Kube-controller-manager    TCP   10252
Read-Only Kubelet API      TCP   10255     

       WORKER
Kubelet API                TCP    10250
Read-only Kubelet API      TCP    10255
NodePort Services          TCP    30000-32767

#Change to root and run installations from there.
```
sudo su
```

#Install docker
```
apt-get install -y docker.io
```

#Create daemon.json file
```
cat << EOF > /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}
EOF
```

#Add repo ky
```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
```

#Add kubernetes.list
```
cat << EOF > /etc/apt/sources.list.d/kubernetes.list
 deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
```

#Run Update and Install The Following
```
apt update -y
apt install -y kubelet kubeadm kubectl
```

#
```
kubeadm init --pod-network-cidr=10.244.0.0/16
```

#you should see a code like this from the kubeinit command
```
kubeadm join 172.31.34.231:6443 --token sdgo5f.xrb5w8gzi8dxtz28 --discovery-token-ca-cert-hash sha256:6b84a4e6c73444e984ba6e5477ca2508d1d9cd83957ff3283dcfbfde0b0e12ae
```

#Run the following commands. Please exit out of root
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

#Start CNI	
```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/v0.9.1/Documentation/kube-flannel.yml
```

#Now run this to see pods and nodes
```
kubectl get nodes
kubectl get pods --all-namespaces
```

     INSTALLING WORKER

On another ubuntu machine which you want to use as worker
Follow through install steps 1&2. Ignore step 3
 
After you run the last command from step 2. you then run the join command to initiate the communication. Please note you should have your own join command generated, so do not use mine
 
#Run the join command from master node
```
kubeadm join 172.31.34.231:6443 --token sdgo5f.xrb5w8gzi8dxtz28 --discovery-token-ca-cert-hash sha256:6b84a4e6c73444e984ba6e5477ca2508d1d9cd83957ff3283dcfbfde0b0e12a
```

At this point you node is ready.
 
#Go to master node and run
```
kubectl get nodes
```
