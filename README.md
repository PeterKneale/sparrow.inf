# sparrow.inf

## Local development

### Docker Compose
```
export DB_DATABASE=sparrow
export DB_USER=postgres
export DB_PASSWORD=password
export DB_PORT=5432
docker-compose up
```

## Cloud Deployment

### Build Docker Images
```
docker build -t simplicate/sparrow-api .
docker images
REPOSITORY               TAG                 IMAGE ID            CREATED             SIZE
simplicate/sparrow-api   latest              1c883fc3ffc6        About an hour ago   681.5 MB
```

### Setup Authentication
[Reference](https://developers.google.com/identity/protocols/application-default-credentials)
[Create credential via console](https://console.developers.google.com/apis/credentials?project=simplicate-sparrow)
```
export GOOGLE_APPLICATION_CREDENTIALS=/xxx/auth.json
```


### Publish Docker Images to Registry
[Reference](https://cloud.google.com/container-registry/docs/pushing)
```
docker tag simplicate/sparrow-api gcr.io/simplicate-sparrow/sparrow-api
gcloud docker push gcr.io/simplicate-sparrow/sparrow-api
gcloud beta container images list
```

### Create Cluster
```
gcloud config set compute/zone us-central1-b
gcloud container clusters create simplicate-sparrow-dev
```

### Run Containers
[Reference](https://cloud.google.com/container-engine/docs/quickstart)
```
kubectl run sparrow-api --image=gcr.io/simplicate-sparrow/sparrow-api --port=80
kubectl expose deployment sparrow-api --type="LoadBalancer"
kubectl get service sparrow-api
```


