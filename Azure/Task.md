# Deploy a container in Azure

The goal of this task is to run a container in Azure.

## Context

You are the lead engineer of a popular mobile application startup that builds an application that performs job placement for engineering jobs and predicts the salary ranges of the job applicants.   Your application does this job placement and salary range prediction using a confidental model trained using a data set of student and employment records.  You're trying to raise another round of funding, and therefore you need to show off the new version of your application.  You've decided to deploy this application to Microsoft Azure using Azure Kubernetes Service.

Keep the context in mind during the experiment.  Security is a factor during the following steps.

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

*Please note, your image name cannot contain capital letters or underscores.*

```
cd kubernetes-hello-world-example
docker build -t <your_image_name> . 
```

**2. Upload your image to an online registry provider.**

You will use the command line to upload your image.

You have the choice of uploading your container in either of the following.  It might be useful to view each documentation using the following links before making your choice.

- [Docker Hub](https://docs.docker.com/engine/reference/commandline/push/) 
- [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-docker-cli) 

Regardless of which choice you make to store your image, you will be able to run the container with Azure Kuberrnetes Service.

- If you choose Azure Container Registry, you'll have to create a new container registry using the CLI.  You are already logged into ACR in the VM that was provided to you.
- If you choose Docker Hub, you'll be using cmeiklejohn's credentials, so your container will have to be named 'cmeiklejohn/<your_image_name>'.  You are already logged into Docker in the VM that was provided to you.

*If you need to change the name of the image, you can rebuild it using `docker build -t` with the new name.*

**3. Create an cluster with Azure Kubernetes Service.**

*If you've already created a resource group earlier, you'd want to use that name here instead of `hello-app`.*

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
kubectl create deployment hello-web --image=<the_image_name>
```

**5. Ensure that the container is running.**

```
kubectl get pods
```

Make sure that the container status is "Running". 

- If you see "ImagePullBackoff", your image could not be pulled. You may have specified the image name incorrectly. Follow step 6 and return to step 4.
- If you see "ErrImgPull", your image could not be pulled because your cluster does not have permissions to the images inside of the container registry.  You may need to search for how to grant access from ACR to your AKS cluster.  Follow step 6, grant any required permissions, and return to step 4 to try again.

**6. Remove the deployment and service.**

```
kubectl delete deployment hello-web
```

**7. Terminate the cluster.**

```
az aks delete --resource-group hello-web --name hello-cluster
az group delete --name hello-web
```
