## Kubernetes Setup

### Running Docker for Mac/Windows?

Yay, it is so easy to setup. Go to `Preferences` -> `Kubernetes` -> `Enable Kubernetes`. That's all!

### Running Docker-Toolbox or Linux?

Then, install [Minikube](https://www.kubernetes.io/docs/tasks/tools/install-minikube).

### Terminology

- `Kubernetes Cluster`  
  A collections of nodes and a master to manage them.

- `Node`  
  A virtual machine that will run our containers.

- `Pod`  
  More or less a running container. Technically, a pod can run multiple containers.

- `Deployment`  
  Monitors a set of pods, make sure they are running and restarts them if they crash.

- `Service`  
  Provides an easy-to-remember URL to access a running container.

## Pod

Example instructions inside a k8s config `posts.yaml`

```
# k8s is extensible - we can add in our own custom objects.
# This specifies the set of objects we want k8s to look at
apiVersion: v1

# The type of object we want to create
kind: Pod

# Config options for the object we are about to create
metadata:

  # When the pod is created, give it a name of 'posts'
  name: posts

# The exact attributes we want to apply to the object we are about to create
spec:

  # We can create many containers in a single pod
  containers:

    # Make a container with a name of 'posts'
    - name: posts

      # The exact image we want to use
      # If we use `:latest` version explicitly or without a version
      # that will use implicit `:latest`, then k8s 1st priority lookup location is DockerHub
      # If we describe `X.X.X` version, k8s will try to look image from local machine
      image: nap/posts:0.0.1
```

### Commands for pod

- Check verison:  
  `kubectl version`

- Tells kubernetes to process the config:  
  `kubectl apply -f XXX.yaml`

- Print out information about all of the running pods:  
  `kubectl get pods`

- Execute the give command in a running pod:  
  `kubectl exec -it [pod_name] [cmd]`

- Print out logs from the given pod:  
  `kubectl logs [pod_name]`

- Deletes the given pod:  
  `kubectl delete pod [pod_name]`

- Print out some information about the runniing pod:  
  `kubectl describe pod [pod_name]`

- Adding timesaving alias.  
  Open `.zshrc` file and add new alias. Here, we will use vscode to edit it:  
  `code ~/.zshrc`

  Add new alias:  
  `alias k="kubectl`

  Activate it:  
  `source ~/.zshrc`

## Deployment

We can say it as `Pod Manager` object. It will be responsible for creating and deleting pods on behalf of us managing pods manually.

Example instructions inside a deployment config:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  # name of deployment
  name: posts-depl
spec:
  # number of pods
  replicas: 1
  # Hey deployment, take a look at pods
  selector:
    # with the label `posts`
    matchLabels:
      # `app` here is nothing to do here, can be any key
      app: posts
  # exact config for pod
  template:
    metadata:
      labels:
        app: posts
    spec:
      containers:
        - name: posts
          image: nap/posts:0.0.1

```

### Commands for deployment

- List all the running deployments:  
  `kubectl get deployments`

  ```
  NAME                          READY   STATUS    RESTARTS   AGE
  posts-depl-5757dd845f-g952t   1/1     Running   0          7s
  ```

  - `READY` is the number of pods we are tryng to create.
  - `1/1` the left hand side number is pods that are up and running and ready to receive some traffic or do work and the right hand side number is number of total pods we are trying to create.

* Print out details about a specific deployment:  
  `kubectl describe deployment [depl name]`

* Create a deployment out of a config file:  
  `kubectl apply -f [config file name]

* Delete a deployment:  
  `kubectl delete deployment [depl_name]

### Updating deployment by #1 changing image version inside deployment config

- k8s knows the applied config, so if we apply a modified version of config file.
- k8s will go to see that the only thing has changed inside modified version of config is the version image or the image of the version (for the case we change our source) and so it's going to make that one single change to our existing deployment as opposed to creating a new deployment.
- We may not see this method often as we need to edit deployment config file everytime which may lead to error

### Updating deployment by #2 using latest version inside deployment config

- Must use `:latest` tag version explicitly or implicitly omit it in deployment config.
- Push the latest image to DockerHub.
- Run the commond to update the deployment:  
  `kubectl rollout restart deployment [depl_name]`

## Services

Services provide networking between pods.  
There are 4 different types of services.

1. Cluster IP
1. Node Port
1. Load Balancer
1. External Name

1) Cluster IP  
   Sets up an easy-to-remember URL to access a pod.  
   Only exposes pods `in the cluster`.

1) Node Port  
   Makes a pod accessible from `outside the cluster`.  
   Usually only used for dev purposes.

1) Load Balancer  
   Makes a pod accessible from `outside the cluster`.  
   This is the right way to expose a pod to the outside world.

1) External Name  
   Redirects an in-cluster request to a CNAME url.

### Commands for service

- Apply service config file:  
  `kubectl apply -f posts-srv.yaml`
- List all services:  
  `kubectl get services`

```
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          3h12m
posts-srv    NodePort    10.110.230.75   <none>        4000:32012/TCP   89s
```

- In PORT(S) column, we can see `:32012` port and that port is randomly assigned NodePort (may be 30XXX or 31XXX or 32XXX).
- That NodePort is actually used to access our service.

### ClusterIP

```
apiVersion: apps/v1
kind: Deployment
metadata:
  # name of deployment
  name: event-bus-depl
...
...
---
apiVersion: v1
kind: Service
metadata:
  name: event-bus-srv
spec:
  selector:
    app: event-bus
  #optional, default will be ClusterIP
  #type: ClusterIP
  ports:
    - name: event-bus
      protocol: TCP
      port: 4005
      targetPort: 4005

```

### NodePort

```
apiVersion: v1
kind: Service
metadata:
  # name of service, need to be different among services of same pod
  # otherwise it will be overwritten to the old one with the new one
  name: posts-srv
spec:
  # service type
  type: NodePort
  # to select pods with label `posts` that we named inside deployment config
  selector:
    app: posts
  # expose the port that the app is listening
  ports:
    # use this name for logging purposes
    - name: posts
      protocol: TCP
      # node service port (inside Node)
      port: 4000
      # actual port that the app is listening
      targetPort: 4000
```

### Load Balancer and Ingress or Ingress Controller

- Load Balancer Service  
  Tells Kubernetes to reach out to its provider and provision a load balancer. Gets traffic in to a single pod

- Ingress or Ingress Controller  
  A pod with a set of routing rules to distribute traffic to other services

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.30.0/deploy/static/mandatory.yaml
```

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.30.0/deploy/static/provider/cloud-generic.yaml
```

ingress-nginx config example:

```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: ingress-srv
  annotations:
    kubernetes.io/ingress.class: nginx
    # add to use regex for dynamic path
    nginx.ingress.kubernetes.io/use-regex: 'true'
spec:
  rules:
    # as we can host many app inside a k8s cluster, we need to clarify the host for our app
    - host: posts.com
      http:
        paths:
          - path: /posts/create
            backend:
              serviceName: posts-clusterip-srv
              servicePort: 4000
          - path: /posts
            backend:
              serviceName: query-srv
              servicePort: 4002
          # nginx doesnt support wild card colon(:), so need to use regex
          - path: /posts/?(.*)/comments
              serviceName: comments-srv
              servicePort: 4001
          # if we are using router in react app, it will be `/?(.*)` instead of `/` here.
          # last not least, wild card route should be at the last order
          - path: /
              serviceName: client-srv
              servicePort: 3000
```
