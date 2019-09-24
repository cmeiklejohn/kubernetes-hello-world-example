# Hello Application example

This example shows how to build and deploy a containerized Go web server
application using [Kubernetes](https://kubernetes.io).

This directory contains:

- `main.go` contains the HTTP server implementation. It responds to all HTTP
  requests with a  `Hello, world!` response.
- `Dockerfile` is used to build the Docker image for the application.

This application is available as two Docker images, which respond to requests

# Running

Here are instructions for running this container.

## Common Instructions

Here's the commands to run the application using a given image.  These instructions
are applicable to any cluster, once deployed on the appropriate cloud provider.

```
$ kubectl create deployment hello-web --image=${IMAGE}
$ kubectl expose deployment hello-web --type=LoadBalancer --port 80 --target-port 8080
```

Now, wait until the service has an external IP address assigned to it.

```
$ kubectl get service | grep LoadBalancer | awk '{print $4}' 
```

Now, ensure the service works.

```
$ curl `kubectl get service | grep LoadBalancer | awk '{print $4}' `
```

Remember to clean up after youself!

```
$ kubectl delete service hello-web
```

## Google Kubernetes Engine

The following bootstraps and terminates a cluster with Google Kubernetes Engine.

```
$ gcloud config set project $PROJECT_ID
$ gcloud config set compute/zone us-east1-b
$ gcloud container clusters create hello-cluster --num-nodes=2
$ gcloud container clusters get-credentials hello-cluster
$ # Common instructions go here.
$ gcloud container clusters delete hello-cluster
```

## Amazon Kubernetes Engine

The following bootstraps and terminates a cluster with Amazon Kubernetes Engine.

```
$ eksctl create cluster --name hello-cluster \
    --version 1.14 \
    --nodegroup-name standard-workers \
    --node-type t3.medium \
    --nodes 3 \
    --nodes-min 1 \
    --nodes-max 4 \
    --node-ami auto
$ # Common instructions go here.
$ eksctl delete cluster --name hello-cluster
```