# kuberneties-master-and-worker-install-
Before start please install docker on all node 

Step 1: Login with root user and Install Docker ( in Master & Worker Node Both)

Step 2:  Create a file with the name containerd.conf using the command:
vim /etc/modules-load.d/containerd.conf

#And add the following lines:
<br>overlay<br>
<br>br_netfilter<br>

#Step 3: Save the file and run the following commands:
<br>modprobe overlay<br>
<br>modprobe br_netfilter<br>

#Step 4: Create a file with the name kubernetes.conf in /etc/sysctl.d folder:
<br>vim /etc/sysctl.d/kubernetes.conf<br>

#Add the following lines in the file and Save it:
<br>net.bridge.bridge-nf-call-ip6tables = 1<br>
<br>net.bridge.bridge-nf-call-iptables = 1<br>
<br>net.ipv4.ip_forward = 1<br>

#Step 5: Run the commands to verify the changes:
<br>sysctl --system<br>
<br>sysctl -p<br>

#Step 6: Remove the config.toml file from /etc/containerd/ Folder and run reload your system daemon:
<br>rm -f /etc/containerd/config.toml<br>
<br>systemctl daemon-reload<br>

#Step 7: Add Kubernetes Repository:
``
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
``
```
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

```
```
sudo apt update

```
```
sudo apt install -y kubeadm=1.28.1-1.1 kubelet=1.28.1-1.1 kubectl=1.28.1-1.1

```
#Step 8: Disable Swap
<br>vim /etc/fstab <br>   (Remove swap mount point from this file)
<br>swapoff -a<br>

#Step 9: Export the environment variable:
<br>export KUBE_VERSION=1.23.0<br>

#Step 10: Install Kubernetes:
<br>apt-get update
apt-get install -y kubelet=${KUBE_VERSION}-00 kubeadm=${KUBE_VERSION}-00 kubectl=${KUBE_VERSION}-00 kubernetes-cni=0.8.7-00
apt-mark hold kubelet kubeadm kubectl
systemctl enable kubelet
systemctl start kubelet<br>

#Step 11: Now it's time to initialize our Cluster!
---MASTER NODE ONLY---
<br>kubeadm init --kubernetes-version=${KUBE_VERSION} (Only on master node)<br>
NOTE:- above command give you 1 output then you have to copy that output in your worker node.
<br>kubeadm token create --print-join-command (To regenrate the tokens)<br>
NOTE:- If your setup is done, and agin you want to add more worker node then you have to generate token by above command

#Step 12:
<br>cp /etc/kubernetes/admin.conf /root/
chown $(id -u):$(id -g) /root/admin.conf
export KUBECONFIG=/root/admin.conf
echo 'export KUBECONFIG=/root/admin.conf' >> /root/.bashrc<br>

#Step 13: Download the daemonset yaml file of required version like following link:
<br>wget https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml<br>

#Step 14: Now apply the daemonset yaml!
<br>kubectl apply -f weave-daemonset-k8s.yaml<br>
