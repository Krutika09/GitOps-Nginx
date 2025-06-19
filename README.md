# Deploy ArgoCD and Configure GitOps
```
minikube status
```

## 1️⃣ Install ArgoCD
```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## 2️⃣ Verify ArgoCD Installation
```sh
kubectl get all -n argocd
```

## 3️⃣ Port Forward ArgoCD Server
```sh
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

🌐 Access ArgoCD Application:
```
http://localhost:8080
```

## 4️⃣ Retrieve ArgoCD Admin Password
```sh
kubectl get secret argocd-initial-admin-secret -n argocd -o yaml | grep password
echo dmlxWWRHcUVMSmZ5VjExbQ== | base64 --decode
```

## 5️⃣ Initialize Git Repository and Push Code
```sh
git init
git branch -m master main
git add .
git commit -m "Initial commit for ArgoCD NGINX deployment"
git remote add origin https://github.com/Krutika09/GitOps-Nginx.git
git pull origin main --rebase
git push -u origin main
```

## 6️⃣ Log in to ArgoCD
```sh
argocd login localhost:8080 --username admin --password sR6uPuLoHeaxOqPq --insecure
```

## 7️⃣ Create and Manage ArgoCD Application
```sh
argocd app create nginx-app --repo https://github.com/Krutika09/GitOps-Nginx.git --path . --dest-server https://kubernetes.default.svc --dest-namespace default
argocd app list
argocd app get nginx-app
```

## 8️⃣ Sync ArgoCD Application
```sh
argocd app sync nginx-app
```
**Error Handling:**  
If you see `"Failed to establish connection"` error, re-run port forwarding and check Minikube status:
```sh
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
Minikube’s Kubelet often stops, so verify:
```sh
minikube status
```

## 9️⃣ Verify NGINX Deployment Health
```sh
argocd app sync nginx-app
```
✅ **Expected Output:**
```
TIMESTAMP                  GROUP        KIND   NAMESPACE                  NAME    STATUS   HEALTH        HOOK  MESSAGE
2025-06-19T09:51:39+05:30            Service     default         nginx-service    Synced  Healthy
2025-06-19T09:51:39+05:30   apps  Deployment     default                 nginx    Synced  Healthy
```

## 🔟 Port Forward NGINX Service and Access UI
```sh
kubectl port-forward svc/nginx-service 8081:80 -n default
```
🌐 **Access Application:**
```
http://localhost:8081
```
---
