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
Create service file 
```bash
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld-one
  namespace: development
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```
```bash
kubectl apply -f hello-app.yaml
```
![image](https://github.com/user-attachments/assets/921da68f-d57f-48fc-940d-f138a0f6c0bd)

