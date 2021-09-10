# ArgoCD Install Along with Deployment with MiniKube
## Install ArgoCD on minkube 
- Create argocd namespace and install the argocd along with crd's 
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
- Download ArgoCD cli : for mac
```
brew install argocd
```
- Change ArgoCD service to LoadBalancer to access the UI :
```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```
- For Minikube forward the port to access as local host
```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
- Now ArgoCD UI should be avaialbe as [localhost:8080](localhost:8080)
- Credentials :
```
user: admin
password: kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
- Login to ArgoCD server using CLI:
```
argocd login \
    --insecure \
    --username admin \
    --password $PASS \
    --grpc-web \
    localhost:8080 
```
- Change password by command line from default to what you want  as following: 
```
argocd account update-password
```
- Now loging to UI again with new Password. 
- *** Argo CD is ready to install the applicaiton now ***

## Install APP for ArgoCD : Simple Hello world way 
- Clone the repo :
```
git clone https://github.com/tiwarisanjay/argo-sample-k8app.git
```
- Kubernates application are in k8s 
- Create a new Namespace to install application 
```
kubectl create namespace devops-toolkit
````
- Run following command to add this application deployment for ArgoCD 
```
argocd app create devops-toolkit \
    --repo https://github.com/vfarcic/devops-toolkit.git \
    --path k8s \
    --dest-server https://kubernetes.default.svc \
    --dest-namespace devops-toolkit
```
- Now go to UI and sync application to deploy. 
- To CleanUp Deployment 
```
argocd app delete devops-toolkit
```
- Validate by running kubectl to makesure nothing is present after deletion :
```
kubectl --namespace devops-toolkit \
    get all
```
- Delete the namespace if you dont need it. 
```
kubectl delete ns devops-toolkit 
```
