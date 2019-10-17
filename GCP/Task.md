# Deploy a container in GCP

The goal of this task is to run a container in GPC.

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
3. Create a Google Kubernetes Engine.
4. Launch the image using Kubernetes on the cluster you created.
5. Ensure that the container is running.
6. Remove teh deployment and service.
7. Terminate the cluster.

In the following paragraphs, we describe each step in detail and, in some steps, we provide commands that you need to execute to complete that subtask. For the subtasks in which we do not specific a command, you need to find those commands online.

**1. Build the image with Docker.**

```
git clone https://github.com/cmeiklejohn/kubernetes-hello-world-example.git
cd kubernetes-hello-world-example
docker build -t <your_image_name> . 
```

**2. Upload your image to an online registry provider.**

You will use the command line to upload your image. You have the choice of uploading your container in "Docker Hub" or "Google Container Registry".

- If you choose Docker Hub, credential will be provided to you. 
- If you choose Google Container Registry, a ``` ${PROJECT_ID} ``` will be provided to you.


**3. Create a Google Kubernetes Engine.**

```
gcloud config set project $PROJECT_ID
gcloud config set compute/zone us-east1-b
gcloud container clusters create hello-cluster --num-nodes=2
gcloud container clusters get-credentials hello-cluster
```

**4. Launch the image using Kubernetes on the cluster you created.**

```
kubectl create deployment hello-web --image=<image_you_uploaded>
kubectl expose deployment hello-web --type=LoadBalancer --port 8080 --target-port 8080
```

**5. Ensure that the container is running.**

```
kubectl get pods
```

Make sure that the container status is "Running". 

If you see "ImagePullBackoff", your image could not be pulled. You may have specified the image name incorrectly. Follow step 6 and return to step 4.

**6. Remove the deployment and service.**

```
kubectl delete service hello-web
kubectl delete deployment hello-web
```

**7. Terminate the cluster.**

```
gcloud container clusters delete hello-cluster
```
