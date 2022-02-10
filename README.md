# Kubernetes
This repository contains information on how to install, setup, and debug Kubernetes.

## Troubelshooting
### Problem 1:\
How to fix "kubeadm init shows kubelet isn't running or healthy"?
or 
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
or 
kubeadm join
```
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
Open the /etc/systemd/system/kubelet.service.d/10-kubeadm.conf file in each node and then add --node-ip=<master or worker node ip > to it. For Example Internal-IP is 192.168.1.1
  
  
 ![image](https://user-images.githubusercontent.com/87664653/153404157-5a4010f0-61e5-4235-94ee-2c40c9e4ed7c.png)
  

And then execute the following commands:
 ```
 sudo systemctl daemon-reload
 sudo systemctl restart kubelet
 ```
