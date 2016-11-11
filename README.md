# Sparrow
Sparrow INF

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

### Create cluster
- Create a cluster
```
gcloud container clusters create sparrow-cluster --scopes bigquery,cloud-platform,cloud-source-repos,cloud-source-repos-ro,compute-ro,compute-rw,datastore,logging-write,monitoring,monitoring-write,service-control,service-management,sql,sql-admin,storage-full,storage-ro,storage-rw,taskqueue,useraccounts-ro,useraccounts-rw,userinfo-email
``` 

### Setup proxy
- Start up a proxy to connect to the Kubernetes control plane:
```
kubectl proxy
``` 
- Goto [http://localhost:8001/ui](http://localhost:8001/ui) and you should be able to see kubernetes


- if you get stale ip's / ssl errors then try this:
```
gcloud container clusters get-credentials sparrow-cluster
``` 
