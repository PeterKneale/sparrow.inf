# sparrow.inf

## Google Cloud Deployment
Instructions for how to deploy the sparrow project onto google cloud infrastructure using kubernetes.

### Setup project
 - Login to [Google Cloud](https://console.cloud.google.com)
 - Create a project 'sparrow' (id: simplicate-sparrow-project)
 
### Install the google cloud tools
 - Install [gcloud](https://www.google.com.au/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjXr5P4i4bQAhWCJ5QKHWY5B40QFggbMAA&url=https%3A%2F%2Fcloud.google.com%2Fsdk%2F&usg=AFQjCNGJ6NuXLC5eFVGtotHysFNTyoS5-Q&sig2=ZjUH_yGAyQgv0HHODP_4kQ&bvm=bv.137132246,d.dGo)
 

### Create cluster

 - Create a cluster
 
    ```
    gcloud container clusters create sparrow-dev
    ```
    
### Setup Authentication
 - Login to your account from the console which will spawn a browser
 
   ```
   gcloud auth application-default login
   ```
   
 - Start up a proxy to connect to the Kubernetes control plane:
 
    ```
    kubectl proxy
    ```
    
 - Goto [http://localhost:8001/ui](http://localhost:8001/ui) and you should be able to see kubernetes

 - Change the project if necessary
 
    ```
    gcloud config set project simplicate-sparrow-project
    ```
    
 - Change the region if necessary
 
    ```
    gcloud config set compute/zone us-central1-b
    ```

### Publish Docker Images to Registry
 - Read the [reference](https://cloud.google.com/container-registry/docs/pushing)

 - Build the docker image using the GOLANG base image (~ 600MB, good for diagnostics)
    ```
    go build
    docker build -t sparrow/api .
    docker tag -f sparrow/api gcr.io/simplicate-sparrow-project/api
    gcloud docker -- push gcr.io/simplicate-sparrow-project/api
    ```

- Build the docker image using the GOLANG:ALPINE base image (~ 200MB and note it is compiled differently) 
    ```
    CC=$(which musl-gcc) go build --ldflags '-w -linkmode external -extldflags "-static"'
    docker build -t sparrow/api-small -f Dockerfile.small .
    docker tag -f sparrow/api-small gcr.io/simplicate-sparrow-project/api-small
    gcloud docker -- push gcr.io/simplicate-sparrow-project/api-small
    ```

- Build the docker image using the ALPINE base image. (~ 10MB and note it is compiled differently)
    ```
    CC=$(which musl-gcc) go build --ldflags '-w -linkmode external -extldflags "-static"'
    docker build -t sparrow/api-tiny -f Dockerfile.tiny .
    docker tag -f sparrow/api-tiny gcr.io/simplicate-sparrow-project/api-tiny
    gcloud docker -- push gcr.io/simplicate-sparrow-project/api-tiny
    ```

- List the remote images
    ```
    gcloud beta container images list
    ```

### Create services, pods and ingress 
 - Read the [quickstart](https://cloud.google.com/container-engine/docs/quickstart)

 - Create the database, api, web and ingress 
    ```
    kubectl create -f db-pod.yaml
    kubectl create -f db-svc.yaml
    
    kubectl create -f api-pod.yaml
    kubectl create -f api-svc.yaml
    
    kubectl create -f web-pod.yaml
    kubectl create -f web-svc.yaml
  
    kubectl create -f ing.yaml
    ```

### View services, pods and ingress 
 - Get the database, api, web and ingress 
    ```
    kubectl describe pod     db-pod
    kubectl describe service db-svc
    
    kubectl describe service api-svc
    kubectl describe pod     api-pod

    kubectl describe pod     web-pod
    kubectl describe service web-service
    
    kubectl describe ing     ing
    ```

### Delete services, pods and ingress 
 - Delete the database, api, web and ingress 
    ```
    kubectl delete service  web-svc
    kubectl delete pod      web-pod

    kubectl delete service  db-svc
    kubectl delete pod      db-pod

    kubectl delete service  api-svc
    kubectl delete pod      api-pod

    kubectl delete ing      ing
    ```

### Delete everything else
- Delete the entire project

    ```
    gcloud projects delete simplicate-sparrow-project
    ```
- delete local containers and images

    ```
    docker rm -f $(docker ps -a -q)
    docker rmi -f $(docker images -q)
    ```
