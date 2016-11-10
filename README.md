# Sparrow
Sparrow INF

<!-- TOC -->

- [Sparrow](#sparrow)
    - [Google Cloud Deployment](#google-cloud-deployment)
        - [Install tools](#install-tools)
        - [Setup project](#setup-project)
        - [Setup auth](#setup-auth)
        - [Setup proxy](#setup-proxy)
        - [Create cluster](#create-cluster)
        - [Create services, pods and ingress](#create-services-pods-and-ingress)
        - [View services, pods and ingress](#view-services-pods-and-ingress)
        - [Delete services, pods and ingress](#delete-services-pods-and-ingress)
        - [Delete Cluster](#delete-cluster)

<!-- /TOC -->
# Application

## Google Cloud Deployment
Instructions for how to deploy the sparrow project onto google cloud infrastructure using kubernetes.

### Install tools
 - Install [gcloud](https://www.google.com.au/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjXr5P4i4bQAhWCJ5QKHWY5B40QFggbMAA&url=https%3A%2F%2Fcloud.google.com%2Fsdk%2F&usg=AFQjCNGJ6NuXLC5eFVGtotHysFNTyoS5-Q&sig2=ZjUH_yGAyQgv0HHODP_4kQ&bvm=bv.137132246,d.dGo)

### Setup project
 - Login to [Google Cloud](https://console.cloud.google.com)
 - Create a project 'sparrow' (id: simplicate-sparrow-project)
 
### Setup auth
- Login to your account from the console which will spawn a browser
```
gcloud auth application-default login
```

### Setup proxy
- Start up a proxy to connect to the Kubernetes control plane:
```
kubectl proxy
``` 
- Goto [http://localhost:8001/ui](http://localhost:8001/ui) and you should be able to see kubernetes


- if you get stale ip's / ssl errors then try this:
```
gcloud container clusters get-credentials sparrow
``` 

### Create cluster

- Create a cluster
```
gcloud container clusters create sparrow
```

### Create services, pods and ingress 
```
kubectl create namespace sparrow-dev
kubectl create namespace sparrow-qa
kubectl create namespace sparrow-prod
kubectl apply -f ./common --namespace sparrow-dev
kubectl apply -f ./common --namespace sparrow-qa
kubectl apply -f ./common --namespace sparrow-prod
kubectl apply -f ./dev --namespace sparrow-dev
kubectl apply -f ./qa --namespace sparrow-qa
kubectl apply -f ./prod --namespace sparrow-prod
```

### View services, pods and ingress 
```
kubectl describe deployment 
kubectl describe service 
kubectl describe ing
```

### Delete services, pods and ingress 
```
kubectl delete deployments --all
kubectl delete pods --all
kubectl delete services --all
```

### Delete Cluster
``` 
gcloud container clusters delete sparrow 
```