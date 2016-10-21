# sparrow.inf

## Docker
Build docker images for use locally or in the cloud
```
docker build -t simplicate/sparrow-api .
docker images
REPOSITORY               TAG                 IMAGE ID            CREATED             SIZE
simplicate/sparrow-api   latest              1c883fc3ffc6        About an hour ago   681.5 MB
```

## Local development
Use docker compose to bring up the local environment.
```
export DB_DATABASE=sparrow
export DB_USER=postgres
export DB_PASSWORD=password
export DB_PORT=5432
docker-compose up
```

## Cloud Deployment
Using Google Cloud, Create a cluster and push instances of our container to it.

### Setup Authentication
[Default credentials](https://developers.google.com/identity/protocols/application-default-credentials)

[Create credential via console](https://console.developers.google.com/apis/credentials?project=simplicate-sparrow)
```
export GOOGLE_APPLICATION_CREDENTIALS=/xxx/auth.json
```

### Publish Docker Images to Registry
[Reference](https://cloud.google.com/container-registry/docs/pushing)

Tag the local image so that it can be pushed to the google container registry.
```
docker tag simplicate/sparrow-api gcr.io/simplicate-sparrow/sparrow-api
```

Push the image to the google container registry
```
gcloud docker push gcr.io/simplicate-sparrow/sparrow-api
```

List the remote images
```
gcloud beta container images list
NAME
gcr.io/simplicate-sparrow/sparrow-api
```

### Create Cluster
Specify the region
```
gcloud config set compute/zone us-central1-b
```

Create a cluster
```
gcloud container clusters create simplicate-sparrow-dev
```

### Run Containers
[Reference](https://cloud.google.com/container-engine/docs/quickstart)

Run the docker image in a container on the cluster
```
kubectl run sparrow-api --image=gcr.io/simplicate-sparrow/sparrow-api --port=80
```

Expose a load balancer in front of the api
```
kubectl expose deployment sparrow-api --type="LoadBalancer"
```

Get the external IP of the new service (may take a few minutes)
```
kubectl get service sparrow-api
NAME          CLUSTER-IP     EXTERNAL-IP     PORT(S)   AGE
sparrow-api   10.3.243.255   104.197.54.72   80/TCP    5m
```

### Connect to the containers
Start up a proxy to connect to the Kubernetes control plane:
```
gcloud container clusters get-credentials simplicate-sparrow-dev --zone us-central1-b --project simplicate-sparrow
kubectl proxy
```
Goto [http://localhost:8001/ui](http://localhost:8001/ui)

