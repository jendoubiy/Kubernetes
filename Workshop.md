# Foobar 

Deploying simple Hello App
Create namespace 'development'
```bash
create namespace development
```
Create Deployment file 
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one
  namespace: development
spec:
  replicas: 2
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
```

## Prérequis

* 2 Go ou plus de RAM par machine. 
* 2 CPU ou plus pour les machines de plan de contrôle.
* Connectivité réseau complète entre toutes les machines du cluster.
* Nom d’hôte unique, adresse MAC.
