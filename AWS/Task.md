# Deploy a container in AWS

The goal of this task is to run a container in AWS.

## Context

You are the CEO of a startup and have been working on your application for the past 3 years. 
Your application will be bought by Google for $1M in the in 2 days.
As part of the purchase, Google wants you to give a live demo of you application to its employees.
You decide to create a Docker container with your application and deploy online for the demo.

This directory contains:

- `main.go` contains your application. It is an HTTP server that responds to all HTTP
  requests with a  `Hello, world!` response.
- `Dockerfile` is used to build the Docker image for the application. This file will copy all contents of this directory in the image.

## Task

The following instructions indicate the steps that need to be followed to complete the task:
1. Build the image with Docker.
2. Upload your image to an online registry provider.
3. Create an Amazon Elastic Kubernetes Service.
4. Launch the image using Kubernetes on the cluster you created.
5. Get an external IP address assigned to the service.
6. Ensure that you can access the running web server.
7. Remove teh deployment and service.
8. Terminate the cluster.

In the following paragraphs, we describe each step in detail and, in some steps, we provide commands that you need to execute to complete that subtask. For the subtasks in which we do not specific a command, you need to find those commands online.

**1. Build the image with Docker.**

```
git clone https://github.com/cmeiklejohn/kubernetes-hello-world-example.git
cd kubernetes-hello-world-example
docker build -t ${IMAGE} . 
```

The ``` ${IMAGE} ``` is the name and optionally the tag, in the ‘name:tag’ format, that you assign to the image.

**2. Upload your image to an online registry provider.**

You will use the command line to upload your image. You have the choice of uploading your container in Docker Hub or AWS.

**3. Create an Amazon Elastic Kubernetes Service.**

```
eksctl create cluster --name hello-cluster --version 1.14 --nodegroup-name standard-workers --node-type t2.micro --nodes 3 --nodes-min 1 --nodes-max 4 --node-ami auto
```

**4. Launch the image using Kubernetes on the cluster you created.**

```
kubectl create deployment hello-web --image=${IMAGE}
kubectl expose deployment hello-web --type=LoadBalancer --port 8080 --target-port 8080
```

**5. Get an external IP address assigned to the service.**

```
kubectl get service | grep LoadBalancer | awk '{print $4}' 
```

**6. Ensure that you can access the running web server.**

```
curl `kubectl get service | grep LoadBalancer | awk '{print $4}' `:8080
```


**7. Remove the deployment and service.**

```
kubectl delete service hello-app
kubectl delete deployment hello-app
```

**8. Terminate the cluster.**

```
eksctl delete cluster --name hello-cluster
```
