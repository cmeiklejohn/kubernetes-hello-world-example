# Deploy a container in Azure

The goal of this task is to run a container in Azure.

## Context

You are the lead engineer of a popular mobile application startup that builds an application that performs job placement for engineering jobs and predicts the salary ranges of the job applicants.   Your application does this job placement and salary range prediction using a confidental model trained using a data set of student and employment records.  You're trying to raise another round of funding, and therefore you need to show off the new version of your application.  You've decided to deploy this application to Microsoft Azure using Azure Kubernetes Service.

Keep the context in mind during the experiment.

This directory contains:

- `main.go` contains your application code. 
- `Dockerfile` is used to build the Docker image for the application. This file will copy all contents of this directory into the image.

## Task

The following instructions indicate the steps that need to be followed to complete the task:
1. Build the image with Docker.
2. Upload your image to an online registry provider.
3. Create a Azure Kubernetes Service.
4. Launch the image using Kubernetes on the cluster you created.
5. Ensure that the container is running.
6. Remove the deployment and service.
7. Terminate the cluster.

In the following paragraphs, we describe each step in detail and, in some steps, we provide commands that you need to execute to complete that subtask. For the subtasks in which we do not specific a command, you need to find those commands online.

**1. Build the image with Docker.**

*Please note, your image name cannot contain and underscore.*

```
git clone https://github.com/cmeiklejohn/kubernetes-hello-world-example.git
cd kubernetes-hello-world-example
docker build -t ${IMAGE} . 
```

The ``` ${IMAGE} ``` is the name and optionally the tag, in the ‘name:tag’ format, that you assign to the image.

**2. Upload your image to an online registry provider.**

You will use the command line to upload your image. 

You have the choice of uploading your container in either of the following.  It might be useful to view each documentation using the following links before making your choice.

- [Docker Hub](https://docs.docker.com/engine/reference/commandline/push/) 
- [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-docker-cli) 

Regardless of which choice you make to you store your image, you will be able to run the container with Azure Kuberrnetes Service.

- If you choose Docker Hub, you'll be using cmeiklejohn's credentials, so your container will have to be named 'cmeiklejohn/<your_image_name>'. 

*If you need to change the name of the image, you can rebuild it using `docker build -t` with the new name.*

**3. Create an cluster with Azure Kubernetes Service.**

```
az group create --name hello-app --location eastus
az aks create --resource-group hello-app \
    --name hello-cluster \
    --node-count 1 \
    --enable-addons monitoring \
    --generate-ssh-keys
az aks get-credentials --resource-group hello-app --name hello-cluster
```

**4. Launch the image using Kubernetes on the cluster you created.**

```
kubectl create deployment hello-web --image=${IMAGE}
```

**5. Ensure that the container is running.**

```
kubectl get pods
```

Make sure that the container status is "Running". 

If you see "ImagePullBackoff", your image could not be pulled. You may have specified the image name incorrectly. Follow step 6 and return to step 4.

**6. Remove the deployment and service.**

```
kubectl delete deployment hello-app
```

**7. Terminate the cluster.**

```
az aks delete --resource-group hello-app --name hello-cluster
az group delete --name hello-app
```
