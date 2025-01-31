###### Controlplane / Master Node

# Zeigt aktuelle und mögliche Versionen aller Komponenten und nächste Befehle an
kubeadm upgrade plan 


# neues Repo anbinden -> z.B. Ändern von 1.30. auf 1.31.
vim /etc/apt/sources.list.d/kubernetes.list

# mögliche installierbare Versionen anzeigen
sudo apt update
sudo apt-cache madison kubeadm
# alternativ:
sudo apt show kubeadm -a 

# kubeadm, kubectl und kubelet upgraden (immer nur eine Minor? Version)
apt upgrade -y kubeadm=1.31.0-1.1 kubectl=1.31.0-1.1 kubelet=1.31.0-1.1

# Zeigt aktuelle und mögliche Versionen aller Komponenten und nächste Befehle an
kubeadm upgrade plan 

# Upgrade Cluster Komponenten (Befehl aus upgrade plan)
kubeadm upgrade apply v1.12.0


# restart services
systemctl restart kubelet

###### Worker Nodes

# Zeigt aktuelle und mögliche Versionen aller Komponenten und nächste Befehle an
kubeadm version

# neues Repo anbinden wenn Sprung von z.B. 1.30. auf 1.31., bei kleineren Sprüngen von 1.30.0 auf 1.30.1 nicht nötig
vim /etc/apt/sources.list.d/kubernetes.list

# mögliche installierbare Versionen anzeigen
sudo apt update
sudo apt-cache madison kubeadm
# alternativ:
sudo apt show kubeadm -a 

# alle Pods von node01 auf andere nodes umplanen -- auf Controlplane
# cordon wird automatisch mitgemacht
kubectl drain node01

# Upgrade kubeadm und kubelet und (kubectl)
apt-get upgrade -y kubeadm=1.12.0-00
apt-get upgrade -y kubelet=1.12.0.0-00

# alternativ
sudo apt-mark unhold kubelet kubectl kubeadm && sudo apt-get update && sudo apt-get install -y kubeadm='1.32.1-*' kubelet='1.32.1-*' kubectl='1.32.1-*' && sudo apt-mark hold kubelet kubectl kubeadm


# Worker Node Upgrade
kubeadm upgrade node 

# restart services
systemctl restart kubelet

# node wieder schedulable gemacht
kubectl uncordon node01

