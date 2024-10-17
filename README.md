# Kubernetes
## Prérequis

* 2 Go ou plus de RAM par machine. 
* 2 CPU ou plus pour les machines de plan de contrôle.
* Connectivité réseau complète entre toutes les machines du cluster.
* Nom d’hôte unique, adresse MAC.

# Install containerd
##Enable IPv4 packet forwarding 


Use the package manager [pip](https://pip.pypa.io/en/stable/) to install foobar.

```bash
# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.ipv4.ip_forward = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system

# Verify that net.ipv4.ip_forward is set to 1
sysctl net.ipv4.ip_forward
```
