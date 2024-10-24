 #   <img src="https://github.com/user-attachments/assets/d567506b-5944-4e20-9517-0cd22e0e2769" width="35" title="hover text"> # Workshop
 
## Configure Master node to lunch pods
By default, the cluster will not schedule pods on the master for security reasons. 
```bash
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
```
![image](https://github.com/user-attachments/assets/828a373c-44b3-4dd1-b78f-6f3c0cc3e3d1)

Create namespace 'development'
```bash
create namespace development
```
![image](https://github.com/user-attachments/assets/b9a1f893-f2e4-4a6a-9c9a-851eeec26621)

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

![image](https://github.com/user-attachments/assets/ca56ef27-fb87-463c-b9f4-e4249afe1341)

![image](https://github.com/user-attachments/assets/811a54c2-d711-4905-8096-d2de7b841d75)

## Installing helm for ubuntu
```bash
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```
## installing Prometheus & Grafana using helm 
Add helm repo
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```
Install the Prometheus helm chart
```bash
helm install prometheus prometheus-community/prometheus 
helm install my-grafana grafana/grafana --namespace development
```
![image](https://github.com/user-attachments/assets/b41005be-41d0-4ca8-b380-065c9f526afc)

![image](https://github.com/user-attachments/assets/4cc4422f-46b2-416c-a270-8118ff311822)

![image](https://github.com/user-attachments/assets/94523b09-f117-478c-a798-7a5769b84c1d)


## Manage kubernetes cluster with k9s
```bash
sudo wget https://github.com/derailed/k9s/releases/download/v0.32.4/k9s_Linux_amd64.tar.gz
tar -C /usr/bin -xvf k9s_Linux_amd64.tar.gz
```
execute k9s tool 
```bash
k9s
```
![image](https://github.com/user-attachments/assets/213424c5-79a2-42cd-8558-326f5b9958c0)

![image](https://github.com/user-attachments/assets/a9be3eca-4237-47cf-8ab9-8337279d484e)


## Management of Kubernetes Objects Using Kustomize
Kustomize is a standalone tool to customize Kubernetes objects through a kustomization file.

Installation of kustomize 
```bash
curl -s "https://raw.githubusercontent.com/kubernetes-sigs/kustomize/master/hack/install_kustomize.sh"  | bash
sudo mv kustomize /usr/local/bin
```
Create Repo 'base'
```bash
mkdir base
```
Create Deployment file 'deployment.yaml'
```bash
vim base/deployment.yaml
```
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: http-test-kustomize
spec:
  selector:
    matchLabels:
      app: http-test-kustomize
  template:
    metadata:
      labels:
        app: http-test-kustomize
    spec:
      containers:
      - name: http-test-kustomize
        image: nginx
        ports:
        - name: http
          containerPort: 8080
          protocol: TCP
```
Create service file 'service.yaml'
```bash
vim base/service.yaml
```
```bash
apiVersion: v1
kind: Service
metadata:
  name: http-test-kustomize
spec:
  ports:
    - name: http
      port: 8080
  selector:
    app: http-test-kustomize
```
Porject Tree :
```bash
vim base/service.yaml
```
Create kustomize file 'kustomization.yaml'
```bash
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

commonLabels:
  app: http-test-kustomize

resources:
  - service.yaml
  - deployment.yaml
```
Project Tree
```bash
|__ base
    |__ deployment.yaml
    |__ service.yaml
    |__ kustomization.yaml
```
apply and generate k8s resources from kustomize
```bash
kubectl apply -k base
```
Result:
```bash
service "http-test-kustomize" deleted
deployment.apps "http-test-kustomize" deleted
```
### Objective
In this exemple, we have used commonLabels and namespece in kustomization.yaml where label env: dev and namespace: development gets added to the customized deployment.yaml.

![image-9](https://github.com/user-attachments/assets/282f12bf-df63-4a8a-989a-a123e16b5ed1)




