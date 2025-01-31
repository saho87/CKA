
# Kommando testen
etcdctl snapshot

# etcd version auf 3 setzen (wenn vorheriges Kommando nicht funzt)
ETCDCTL_API=3 # nur für nächsten Befehl
export ETCDCTL_API=3 # für ganze Sitzung der Shell

# Kommando müsste jetzt klappen
etcdctl snapshot

# --endpoint entspricht--listen-client-urls (nur IP)
# --cacert entspricht --trusted-ca-file
# --cert entspricht --cert-file
# --key entspricht --key-file
# Pfad am Ende entspricht Ablageort dese Backups

###### Backup

etcdctl snapshot save --endpoints=127.0.0.1:2379 \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key /opt/etcd-backup.db

###### Restore

# data-dir muss neue Verzeichnis sein!
# cluster2.db ist backup
etcdctl --data-dir /var/lib/etcd-data-new snapshot restore /opt/cluster2.db 

# wenn etc als static pod deployed
cd /etc/kubernetes/manifests
mv *.yaml ll
# Ändern des Volumes (hostspath) auf neues data-dir
mv ../*.yaml .

# wenn etc extern als service deployed
mkdir -p /var/lib/etcd-data-new 
chown -R etcd:etcd /var/lib/etcd-data-new
# ändern des data-dirs in vim /etc/systemd/system/etcd.*
systemctl daemon reload
systemctl restart etcd