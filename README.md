# Kubernetes
This repository contains information on how to install, setup, and debug Kubernetes.
## Installation
### Docker Installation on Ubuntu
1.Install Docker on the master and worker nodes.
```
sudo apt-get update
```
2.
```
sudo apt-get install ca-certificates curl gnupg lsb-release    
```
3.
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
4.
```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
5.
```
sudo apt-get update
```
6.
```
sudo apt-get install docker-ce docker-ce-cli containerd.io
```
7.
```
sudo systemctl enable docker 
```
8. Verfiy installation
```
docker ––version
```
Read more information about the commands at the following link:

https://docs.docker.com/engine/install/ubuntu/
### kubernetes Installation on Ubuntu
1.
```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system
```
2.
```
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
```
3.
```
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
```
4.
```
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

```
5.
```
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```
6.
```
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```
7.
```
Create this file "daemon.json" in the directory "/etc/docker" and add the following
{
"exec-opts": ["native.cgroupdriver=systemd"]
}
```
8.
```
sudo systemctl restart docker
sudo kubeadm reset
```
## Kubernetes Troubleshooting
### Problem 1:\
How to fix "kubeadm init shows kubelet isn't running or healthy"? \
or \
How to fix "The HTTP call equal to 'curl -sSL http://localhost:10248/healthz' failed with error: Get "http://localhost:10248/healthz": dial tcp 127.0.0.1:10248: connect: connection refused." ? 
```
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```
and then reboot
 ```
Create this file "daemon.json" in the directory "/etc/docker" and add the following
{
"exec-opts": ["native.cgroupdriver=systemd"]
}
sudo systemctl restart docker
sudo kubeadm reset
and then Initialize or join command 
kubeadm init ... 
```
FYI,
https://phoenixnap.com/kb/install-kubernetes-on-ubuntu
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
### Problem 2
how to fix "The connection to the server localhost:8080 was refused - did you specify the right host or port"?
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
### Problem 3
How to remove kubernets? 
```
sudo kubeadm reset
sudo apt-get purge kubeadm kubectl kubelet kubernetes-cni kube*   
sudo apt-get autoremove  
sudo rm -rf ~/.kube
```
And restart the computer.
### Problem 4
How to changes Internal-IP for kubernetes master and worker nodes? \
Open the /etc/systemd/system/kubelet.service.d/10-kubeadm.conf file in each node and then add --node-ip=<master or worker node ip > to it. \
For example my Internal-IP is 192.168.1.2. \
Therefore I added --node-ip=192.168.1.2 to the end of the Environment =... line.
 
  
 ![image](https://user-images.githubusercontent.com/87664653/153404157-5a4010f0-61e5-4235-94ee-2c40c9e4ed7c.png)
  

And then execute the following commands:
 ```
 sudo systemctl daemon-reload
 sudo systemctl restart kubelet
 ```
## Problem 5
How to fix Kubernetes stuck in ContainerCreating?

For me, it happened because I forgot to deploy a pod network to the cluster.

I used the https://docs.projectcalico.org/manifests/calico.yaml file.Therefore, I executed the following command. 
 ```
 sudo kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
 ```
The status of my Pods is then changed to "Running."
 
The other network model can be found at the following link.
 
 https://kubernetes.io/docs/concepts/cluster-administration/networking/
 
### Problem 6
  
How to fix "MountVolume.SetUp failed for volume "kube-api-access-xjtgm" : object "default"/"kube-root-ca.crt" not registered" \
Add "automountServiceAccountToken: false" to the pod creation yaml file.
```
apiVersion: v1
kind: Pod
metadata:
  name: my-pv-pod
spec:
  automountServiceAccountToken: false
  restartPolicy: Never
  containers:
    - name: busybox
      image: busybox
...  
```
  
 Give me a star :) if this repository helps you to solve the problem.
