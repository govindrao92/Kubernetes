To install kubeadm on a CentOS 9 machine, follow these steps:

Step 1: Disable SELinux
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config


Step 2: Disable Swap
sudo swapoff -a
sudo sed -i '/swap/d' /etc/fstab


Step 3: Load Kernel Modules
sudo modprobe overlay
sudo modprobe br_netfilter

Ensure these modules load at startup:
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF


Step 4: Configure sysctl Parameters
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

Apply changes:
sudo sysctl --system


Step 5: Install Required Packages
sudo dnf install -y yum-utils


Step 6: Add Kubernetes Repository
sudo cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/repodata/repomd.xml.key
exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni
EOF


Step 7: Install kubeadm, kubelet, and kubectl
sudo dnf install -y kubelet kubeadm kubectl --disableexcludes=kubernetes


Step 8: Enable and Start kubelet
sudo systemctl enable --now kubelet


Step 9: Install Container Runtime
You can install Containerd as the container runtime:
Install Containerd
sudo dnf install -y containerd

Configure containerd:
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml > /dev/null

Set systemd cgroup driver (Required for Kubernetes):
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml

Restart containerd:
sudo systemctl restart containerd
sudo systemctl enable containerd


Step 10: Initialize Kubernetes Cluster (If Needed)
If this is the master node, initialize the cluster:
sudo kubeadm init --pod-network-cidr=192.168.0.0/16

Follow the output instructions to configure kubectl access.

Step 11: Deploy a Network Plugin
For a simple setup, install Calico:
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/tigera-operator.yaml
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/custom-resources.yaml

