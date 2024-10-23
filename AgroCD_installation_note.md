## ArgoCD (Kubernetes)

Install argoCD through ArgoCD Documentation 

Doc: [DOCUMENTATION LINK](https://argo-cd.readthedocs.io/en/stable/?_gl=1*vb89rf*_ga*MTgyNjgzMTcxMy4xNzI5NjgwNjkw*_ga_5Z1VTPDL73*MTcyOTY4MDY4OS4xLjAuMTcyOTY4MDY5NC4wLjAuMA..)


After installing follow below steps 


#### A Simple way to access ArgoCD UI 

```
kubectl port-forward svc/argocd-server -n argocd 8080:443 --address 0.0.0.0 &
```

#### via LB also we can access but you need to run it on a cloud platform
go to 
```
kubectl edit svc argocd-server -n argocd
```

> Then change clusterIP to NodePort or LodeBalancer service and save

> If using nodeport then follow the below command<br/>
```
minikube service argocd-server -n argocd
```

#### To extract the password  
```
kubectl get secret -n argocd
```
```
kubectl edit secret argocd-initial-admin-secret -n argocd
```
```
echo cVY2RTBPRElrVEtqZGdabA== | base64 --decode 
```

CLI is also there<br/>
argocd command refernce type in google 

For reference watch the below video <br/>
https://www.youtube.com/watch?v=ZgJQG475oME 
