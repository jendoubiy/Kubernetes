 #   <img src="https://github.com/user-attachments/assets/d567506b-5944-4e20-9517-0cd22e0e2769" width="35" title="hover text"> # Workshop 

Create namespace 'development'
```bash
create namespace development
```
## Deploying simple Hello App
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

![image](https://github.com/user-attachments/assets/811a54c2-d711-4905-8096-d2de7b841d75)

## Installing helm for ubuntu
```bash
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```
## installing Prometheus using helm 
Add helm repo
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```
Install the Prometheus helm chart
```bash
helm install prometheus prometheus-community/prometheus
```
![image](https://github.com/user-attachments/assets/b41005be-41d0-4ca8-b380-065c9f526afc)

## Manage kubernetes cluster with k9s


