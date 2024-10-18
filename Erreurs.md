 #   <img src="https://github.com/user-attachments/assets/d567506b-5944-4e20-9517-0cd22e0e2769" width="35" title="hover text"> Kubernetes - Troubleshooting

## Error during kubeadm init
kubeadm blocks waiting for control plane during installation
```bash
[apiclient] Created API client, waiting for the control plane to become ready
```
* network connection problems. Check that your machine has full network connectivity before continuing.
* the cgroup driver of the container runtime differs from that of the kubelet. To understand how to configure it properly, see Configuring a cgroup driver.
* control plane containers are crashlooping or hanging.
