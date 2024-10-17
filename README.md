  <img src="https://github.com/user-attachments/assets/d567506b-5944-4e20-9517-0cd22e0e2769" width="35" title="hover text"> # Kubernetes 

## Prérequis

* 2 Go ou plus de RAM par machine. 
* 2 CPU ou plus pour les machines de plan de contrôle.
* Connectivité réseau complète entre toutes les machines du cluster.
* Nom d’hôte unique, adresse MAC.

# Install containerd ( Master & Worker nodes )
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
uninstall all conflicting packages:
```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```
Set up Docker's apt repository.
```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
Install the Docker packages.
```bash
 sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## Configuring the systemd cgroup driver ( Master & Worker nodes )
To use the systemd cgroup driver in /etc/containerd/config.toml with runc, set
```bash
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    SystemdCgroup = true
```
```bash
# restart containerd service
sudo systemctl restart containerd
```
# Installing kubeadm, kubelet and kubectl  ( Master & Worker nodes )

1 - Update the apt package index and install packages needed to use the Kubernetes apt repository:
```bash
sudo apt-get update
# apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
```
2 - Download the public signing key for the Kubernetes package repositories. The same signing key is used for all repositories so you can disregard the version in the URL:
```bash
# If the directory `/etc/apt/keyrings` does not exist, it should be created before the curl command, read the note below.
# sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```
3 - Add the appropriate Kubernetes apt repository.
```bash
# This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
4 - Update the apt package index, install kubelet, kubeadm and kubectl, and pin their version:
```bash
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```
5 - (Optional) Enable the kubelet service before running kubeadm:
```bash
sudo systemctl enable --now kubelet
```
# Initializing Master node
```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=10.5.13.177
```
To start using your cluster, you need to run the following as a regular user:
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
# Using Weave Net on Kubernetes ( Master node )
Weave Net provides networking and network policy, and does not require an external database
```bash
kubectl apply -f https://reweave.azurewebsites.net/k8s/v1.29/net.yaml
```
# Join Worker Node ( Worker node )

```bash
kubeadm join 10.5.13.177:6443 --token tc03nq.nwh5wkojg319vpaa \
	--discovery-token-ca-cert-hash sha256:869b91d31611b0f64ae08d402bad1a5be636b8cfd5b98997381bdb408bd1a1d5
```
