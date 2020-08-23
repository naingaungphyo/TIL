# Kubernetes Cluster on Google Cloud Platform

### Install kubectl (Not running Docker)

`gcloud components install kubectl`

### Connect cluster (Installing GCloud Context)

`gcloud container clusters get-credentials <cluster name>`

### gcloud credentials

`gcloud auth application-default login`

### ingress-nginx

[Ingress Docs](kubernetes.github.io/ingress-nginx/deploy)

- Mandatory Command  
  `kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.30.0/deploy/static/mandatory.yaml`

- GCE-GKE specific ingress  
  `kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.30.0/deploy/static/provider/cloud-generic.yaml`
