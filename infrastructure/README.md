
# Running jenkins
- Taken from here https://cloud.google.com/solutions/jenkins-on-container-engine-tutorial

```
gcloud compute images create jenkins-home-image --source-uri https://storage.googleapis.com/solutions-public-assets/jenkins-cd/jenkins-home-v2.tar.gz
gcloud compute disks create jenkins-home --image jenkins-home-image

kubectl create secret generic jenkins --from-file=./jenkins/options --namespace=infrastructure
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /tmp/tls.key -out /tmp/tls.crt -subj "/CN=jenkins/O=jenkins"
kubectl create secret generic tls --from-file=/tmp/tls.crt --from-file=/tmp/tls.key --namespace infrastructure

kubectl apply -f ./jenkins

```