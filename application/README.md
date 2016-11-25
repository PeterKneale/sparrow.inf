
# Application

### Create services,pods and ingress 
```
kubectl create namespace infrastructure
kubectl create namespace sparrow-dev
kubectl create namespace sparrow-qa
kubectl create namespace sparrow-prod
kubectl apply -f ./common  --namespace sparrow-dev
kubectl apply -f ./common  --namespace sparrow-qa
kubectl apply -f ./common  --namespace sparrow-prod
kubectl apply -f ./dev     --namespace sparrow-dev
kubectl apply -f ./qa      --namespace sparrow-qa
kubectl apply -f ./prod    --namespace sparrow-prod
```

### View services,pods and ingress 
```
kubectl describe deployment 
kubectl describe service 
kubectl describe ing
```

### Delete services,pods and ingress 
```
kubectl delete deployments --all --namespace sparrow-dev
kubectl delete pods        --all --namespace sparrow-dev
kubectl delete services    --all --namespace sparrow-dev
BEST NOT TO DELETE INGRESS BECAUSE IPS CHANGE
# kubectl delete ingress     --all --namespace sparrow-dev 
```


### Delete Cluster
``` 
gcloud container clusters delete sparrow-cluster
```