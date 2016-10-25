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

### Setup Authentication
 - Instructions for creating your [default credentials](https://developers.google.com/identity/protocols/application-default-credentials)
   in your [console](https://console.developers.google.com/apis/credentials?project=simplicate-sparrow)
 - Once you have your authentication file, setup the below environment variable point to it.
   ```
   export GOOGLE_APPLICATION_CREDENTIALS=/xxx/auth.json
   ```
 - Login to your account from the console which will spawn a browser
    ```
    gcloud auth login
    ```
 - Specify the project if necessary
    ```
    gcloud config set project simplicate-sparrow
    ```
 - Specify the region
    ```
    gcloud config set compute/zone us-central1-b
    ```

### Publish Docker Images to Registry
 - Read the [reference](https://cloud.google.com/container-registry/docs/pushing)
 - Build the docker image
    ```
    docker build -t simplicate/sparrow-api .
    ```

 - Tag the local image so that it can be pushed to the google container registry.
    ```
    docker tag simplicate/sparrow-api gcr.io/simplicate-sparrow/sparrow-api
    ```

- Push the image to the google container registry
    ```
    gcloud docker -- push gcr.io/simplicate-sparrow/sparrow-api
    ```

- List the remote images
    ```
    gcloud beta container images list
    ```

### Create cluster

 - Create a cluster
    ```
    gcloud container clusters create simplicate-sparrow-dev
    ```

### Run Containers
 - Read the [quickstart](https://cloud.google.com/container-engine/docs/quickstart)

 - Create the database 
    ```
    kubectl create -f database-pod.yaml
    kubectl create -f database-service.yaml
    ```

 - Run the docker image in a container on the cluster
    ```
    kubectl run sparrow-api --image=gcr.io/simplicate-sparrow/sparrow-api --port=80
    ```

 - Expose a load balancer in front of the api
    ```
    kubectl expose deployment sparrow-api --type="LoadBalancer"
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
    gcloud container clusters get-credentials simplicate-sparrow-dev --zone us-central1-b --project simplicate-sparrow

    kubectl proxy
    ```
 - Goto [http://localhost:8001/ui](http://localhost:8001/ui) and you should be able to see kubernetes

https://blog.oestrich.org/2015/08/running-postgres-inside-kubernetes/
