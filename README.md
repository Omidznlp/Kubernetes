# Kubernetes
This repository contains information on how to install, setup, and debug Docker and Kubernetes.
## Installation
### Docker Installation on Ubuntu
Install Docker on the master and worker nodes.\
1.
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
Install kubernetes on the master and worker nodes.\
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
7. Create this file "daemon.json" in the directory "/etc/docker" and add the following
```
{
"exec-opts": ["native.cgroupdriver=systemd"]
}
```
8.
```
sudo systemctl restart docker
sudo kubeadm reset
```
9. The following procedure should only be applied on the master node, and only the "kubeadm join" command should be executed on worker nodes. 
```
sudo kubeadm init --pod-network-cidr=10.10.0.0/16 --apiserver-advertise-address=<The IP address of your master node can be used by worker nodes to connect to it for example:192.168.1.5>
```
Seeing the following lines at the terminal
```
[init] Using Kubernetes version: v1.23.5
[preflight] Running pre-flight checks
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [jenkins-agent kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 10.0.2.15]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [jenkins-agent localhost] and IPs [10.0.2.15 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [jenkins-agent localhost] and IPs [10.0.2.15 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 10.509242 seconds
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.23" in namespace kube-system with the configuration for the kubelets in the cluster
NOTE: The "kubelet-config-1.23" naming of the kubelet ConfigMap is deprecated. Once the UnversionedKubeletConfigMap feature gate graduates to Beta the default name will become just "kubelet-config". Kubeadm upgrade will handle this transition transparently.
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node jenkins-agent as control-plane by adding the labels: [node-role.kubernetes.io/master(deprecated) node-role.kubernetes.io/control-plane node.kubernetes.io/exclude-from-external-load-balancers]
[mark-control-plane] Marking the node jenkins-agent as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: qp794c.xbsi5nanw2u9sn9x
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:
kubeadm join 192.168.1.5:6443 --token qp794c.xbsi5nanw2u9sn9x \
	--discovery-token-ca-cert-hash sha256:355a4ca26e908ddc939b8476377f17d1133a80132eab7db07655f6fa2bacd6e2 

```
10.
```
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
11. To deploy the Network model
```
sudo kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```
The other network model can be found at the following link.\
 
 https://kubernetes.io/docs/concepts/cluster-administration/networking/
 
12. To join worker nodes to the cluster, please insert the following command in the worker nodes. 

```
kubeadm join 192.168.1.5:6443 --token qp794c.xbsi5nanw2u9sn9x \
	--discovery-token-ca-cert-hash sha256:355a4ca26e908ddc939b8476377f17d1133a80132eab7db07655f6fa2bacd6e2 
```

FMI,
https://phoenixnap.com/kb/install-kubernetes-on-ubuntu

https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

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
  
 Problem 7
 How to fix the following problem?
 sudo kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml? \
 The connection to the server localhost:8080 was refused - did you specify the right host or port?
 ```
 mkdir -p $HOME/.kube
 sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
 sudo chown $(id -u):$(id -g) $HOME/.kube/config
 ```
  
 Give me a star :) if this repository helps you to solve the problem.
