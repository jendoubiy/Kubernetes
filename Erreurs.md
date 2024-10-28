 #   <img src="https://github.com/user-attachments/assets/d567506b-5944-4e20-9517-0cd22e0e2769" width="35" title="hover text"> Kubernetes - Troubleshooting

## Error during kubeadm init
kubeadm blocks waiting for control plane during installation
```bash
[apiclient] Created API client, waiting for the control plane to become ready
```
* network connection problems. Check that your machine has full network connectivity before continuing.
* the cgroup driver of the container runtime differs from that of the kubelet. To understand how to configure it properly, see Configuring a cgroup driver.
* control plane containers are crashlooping or hanging.
  
 you can try running kubeadm init with the --ignore-preflight-errors flag to ignore any preflight errors
 * Directory Availability Errors
 * User Privilege Errors
 * Swap Errors
 * CPU Availability Errors
 * File Content Errors
```bash
kubeadm init --ignore-preflight-errors
```
## Error starting kubelet service
swap is not supported by default on Kubernetes. 
```bash
● kubelet.service - kubelet: The Kubernetes Node Agent
   Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
  Drop-In: /etc/systemd/system/kubelet.service.d
           └─10-kubeadm.conf
   Active: activating (auto-restart) (Result: exit-code) since Tue 2020-06-16 11:46:05 UTC; 9s ago
     Docs: https://kubernetes.io/docs/home/
  Process: 28012 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_ARGS (code=exited, status=255)
 Main PID: 28012 (code=exited, status=255)

Jun 16 11:46:05 k8s-l2bridge-ma systemd[1]: kubelet.service: Failed with result 'exit-code'.
```
 1 / Restart kubelet service 

```bash
systemctl restart kubelet
```
 2 / swap is not supported by default on Kubernetes. 
 ```bash
[kubeadm] WARNING: kubeadm is in beta, please do not use it for production clusters.
[init] Using Kubernetes version: v1.8.2
[init] Using Authorization modes: [Node RBAC]
[preflight] Running pre-flight checks
[preflight] WARNING: Connection to "https://192.168.78.48:6443" uses proxy "http://user:pwd@192.168.78.15:3128/". If that is not intended, adjust your proxy settings
[preflight] WARNING: Running with swap on is not supported. Please disable swap or set kubelet's --fail-swap-on flag to false.
```
Solution :
disable the swap in the machine with :
```bash
swapoff -a
```

## TLS certificate errors
```bash
# kubectl get pods
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "kubernetes")
```
Solution
* Verify that the $HOME/.kube/config file contains a valid certificate
* Unset the KUBECONFIG environment variable using:
```bash
unset KUBECONFIG
```
Or set it to the default KUBECONFIG location:
```bash
export KUBECONFIG=/etc/kubernetes/admin.conf
```
## Docker Repository Does Not Have a Release File on Running apt-get update on Ubuntu 
```bash
W: The repository 'https://apt.dockerproject.org/repo ubuntu-xenial Release' does not have a Release file.
N: Data from such a repository can't be authenticated and is therefore potentially dangerous to use.
N: See apt-secure(8) manpage for repository creation and user configuration details.
E: Failed to fetch https://apt.dockerproject.org/repo/dists/ubuntu-xenial/main/binary-amd64/Packages
E: Some index files failed to download. They have been ignored, or old ones used instead.
```
Solution 
```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
replace this $(. /etc/os-release && echo "$VERSION_CODENAME") with the latest stable debian releas

