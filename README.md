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

```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```
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
```
vim /etc/fstab
```

   (Remove swap mount point from this file)
   ```
swapoff -a
```
#Step 10: Install Kubernetes:
```
apt-mark hold kubelet kubeadm kubectl
systemctl enable kubelet
systemctl start kubelet
```

#Step 11: Now it's time to initialize our Cluster!
---MASTER NODE ONLY---
<br>kubeadm init --kubernetes-version=${KUBE_VERSION} (Only on master node)<br>
NOTE:- above command give you 1 output then you have to copy that output in your worker node.
```
kubeadm token create --print-join-command
``` (To regenrate the tokens)<br>
NOTE:- If your setup is done, and agin you want to add more worker node then you have to generate token by above command

#Step 12:
```
cp /etc/kubernetes/admin.conf /root/
chown root:$(id -g) /root/admin.conf
export KUBECONFIG=/root/admin.conf
echo 'export KUBECONFIG=/root/admin.conf' >> /root/.bashrc

```

#Step 13: Download the daemonset yaml file of required version like following link:
```
wget https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```

#Step 14: Now apply the daemonset yaml!
```
kubectl apply -f weave-daemonset-k8s.yaml
```
