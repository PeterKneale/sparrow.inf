# sparrow.inf

## Local development
Use docker compose to bring up the local environment.
```
export DB_DATABASE=sparrow
export DB_USER=postgres
export DB_PASSWORD=password
docker-compose up
```

## Cloud Deployment
Using [Google Cloud](https://console.cloud.google.com), create a cluster and push instances of our container to it.

### Setup project
 - Create your project 'sparrow' (simplicate-sparrow-project)

 - To check your configuration
 ```
 gcloud config list
 ```

### Setup Authentication
 - Login to your account from the console which will spawn a browser
   ```
   gcloud auth application-default login
   ```
 - Specify the project if necessary
    ```
    gcloud config set project simplicate-sparrow-project
    ```
 - Specify the region
    ```
    gcloud config set compute/zone us-central1-b
    ```

### Publish Docker Images to Registry
 - Read the [reference](https://cloud.google.com/container-registry/docs/pushing)
 - Build the docker image
    ```
    docker build -t sparrow/api .
    docker build -t sparrow/api:small  -f Dockerfile.small .
    docker build -t sparrow/api:tiny   -f Dockerfile.tiny .
    ```

 - Tag the local image so that it can be pushed to the google container registry.
    ```
    docker tag sparrow/api        gcr.io/simplicate-sparrow-project/api
    docker tag sparrow/api:small  gcr.io/simplicate-sparrow-project/api:small
    docker tag sparrow/api:tiny   gcr.io/simplicate-sparrow-project/api:tiny
    ```

- Push the image to the google container registry
    ```
    gcloud docker -- push gcr.io/simplicate-sparrow-project/api
    gcloud docker -- push gcr.io/simplicate-sparrow-project/api:small
    gcloud docker -- push gcr.io/simplicate-sparrow-project/api:tiny
    ```

- List the remote images
    ```
    gcloud beta container images list
    ```

### Create cluster

 - Create a cluster
    ```
    gcloud container clusters create sparrow-dev
    ```

### Run Containers
 - Read the [quickstart](https://cloud.google.com/container-engine/docs/quickstart)

 - Create the database 
    ```
    kubectl create -f database-pod.yaml
    kubectl create -f database-service.yaml
    ```
 - Create the api 
    ```   
    kubectl create -f api-pod.yaml
    kubectl create -f api-service.yaml
    ```
- Allow ingress (allow 5 mins)
    ```
    kubectl create -f ingress.yaml
    ```

 - Get the external IP of the new service (may take a few minutes)
    ```
    kubectl get service sparrow-api
    NAME          CLUSTER-IP     EXTERNAL-IP     PORT(S)   AGE
    sparrow-api   10.3.243.255   104.197.54.72   80/TCP    5m
    ```

### Connect to the containers via the Kubernetes Control Plane Proxy Thing
 - Start up a proxy to connect to the Kubernetes control plane:
    ```
    gcloud container clusters get-credentials sparrow-dev --zone us-central1-b --project sparrow
    kubectl proxy
    ```
 - Goto [http://localhost:8001/ui](http://localhost:8001/ui) and you should be able to see kubernetes

## Teardown
- Delete the entire project
    ```
    gcloud projects delete sparrow
    ```
 - or
    ```
    kubectl delete pod sparrow-db
    kubectl delete service sparrow-db
    kubectl delete pod sparrow-api
    kubectl delete service sparrow-api
    kubectl delete ing sparrow-ingress
    ```
# References
 - https://blog.oestrich.org/2015/08/running-postgres-inside-kubernetes/
