### Installation eines Kubernetes Clusters

# Doku
https://kubernetes.io/docs/setup/production-environment/container-runtimes/
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
https://kubernetes.io/docs/concepts/cluster-administration/addons/


# install of kubeadm (every node)
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet=1.31.0-1.1 kubeadm=1.31.0-1.1 kubectl=1.31.0-1.1
sudo apt-mark hold kubelet kubeadm kubectl

# IPv4 Forwarding aktivieren (every node)

sysctl net.ipv4.ip_forward=1

# Apply sysctl params without reboot
sudo sysctl --system

# install of containerd (every node)
sudo apt update
sudo apt install -y containerd

# set systemd as default
sudo mkdir -p /etc/containerd
containerd config default | sed 's/SystemdCgroup = false/SystemdCgroup = true/' | sudo tee /etc/containerd/config.toml
sudo systemctl restart containerd

# kubeadm tool starten - nur auf controlplane (weitere Instruktionen in Consolen Output)
sudo kubeadm init --apiserver-advertise-address 192.168.56.11 --pod-network-cidr "10.244.0.0/16" --apiserver-cert-extra-sans controlplane

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Netzwerk install (z.B. flannel oder weave.net)
https://kubernetes.io/docs/concepts/cluster-administration/addons/

# worker-Nodes hinzufügen (auf worker nodes)
kubeadm join 192.168.178.90:6443 --token ypbpdo.vciu7h1zbb00ltz2 \
	--discovery-token-ca-cert-hash sha256:3716ad7afdb042fd87aef72f6b3509148cae03ca21bc187b6fa093262e134ac3 

# wenn Token existiert/ Commando nochmal ausgegeben werden soll
kubeadm token create --print-join-command

# wenn Nodes gleiche IP Adressen haben
vim /etc/default/kubelet -> KUBELET_EXTRA_ARGS=--node-ip=ABCXYZ

