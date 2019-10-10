# Hello Application example

This example shows how to build and deploy a containerized Go web server
application using [Kubernetes](https://kubernetes.io).

This directory contains:

- `main.go` contains the HTTP server implementation. It responds to all HTTP
  requests with a  `Hello, world!` response.
- `Dockerfile` is used to build the Docker image for the application.

This application is available as two Docker images, which respond to requests

## Installation and Running Container

Here are instructions for building and running this container on Kubernetes.

### Docker

First, we need to build the container with Docker.

```
git clone https://github.com/cmeiklejohn/kubernetes-hello-world-example.git
cd kubernetes-hello-world-example
docker build -t ${IMAGE} . 
```

### Kubernetes

Once you bootstrap a cluster, the following commands can be used to launch an image using Kubernetes on that cluster, regardless of the cloud provider.

```
kubectl create deployment hello-web --image=${IMAGE}
kubectl expose deployment hello-web --type=LoadBalancer --port 8080 --target-port 8080
```

Now, wait until the service has an external IP address assigned to it.  This process, depending on the cloud provider, could take a number of minutes to complete.

```
kubectl get service | grep LoadBalancer | awk '{print $4}' 
```

Now, ensure the service works by using `curl` to access the web server.  Make sure you see the proper response from the service.  You may have to wait for DNS to update to allow the hostname to resolve.

```
curl `kubectl get service | grep LoadBalancer | awk '{print $4}' `:8080
```

Remember to clean up after youself and remove the deployment and service.

```
kubectl delete service hello-app
kubectl delete deployment hello-app
```

### Google Kubernetes Engine

Here are installation instructions for Ubuntu.

```
echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] http://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key --keyring /usr/share/keyrings/cloud.google.gpg add -
sudo apt-get update && sudo apt-get install google-cloud-sdk
gcloud components install kubectl
```

The following bootstraps a cluster with Google Kubernetes Engine.

```
gcloud config set project $PROJECT_ID
gcloud config set compute/zone us-east1-b
gcloud container clusters create hello-cluster --num-nodes=2
gcloud container clusters get-credentials hello-cluster
```

The following terminates a cluster with Google Kubernetes Engine.

```
gcloud container clusters delete hello-cluster
```

### Amazon Elastic Kubernetes Service

Here are installation instructions for Ubuntu.

```
pip install awscli --upgrade --user
aws configure
curl --silent --location "https://github.com/weaveworks/eksctl/releases/download/latest_release/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
```

The following bootstraps a cluster with Amazon Elastic Kubernetes Service.

```
eksctl create cluster --name hello-cluster --version 1.14 --nodegroup-name standard-workers --node-type t3.medium --nodes 3 --nodes-min 1 --nodes-max 4 --node-ami auto
```

The following terminates a cluster with Amazon Elastic Kubernetes Service.

```
eksctl delete cluster --name hello-cluster
```

### Microsoft Azure Kubernetes Service

Here are installation instructions for Ubuntu.

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
az login
az aks install-cli
```

The following bootstraps a cluster with Azure Kubernetes Service.

```
az group create --name hello-app --location eastus
az aks create --resource-group hello-app \
    --name hello-cluster \
    --node-count 1 \
    --enable-addons monitoring \
    --generate-ssh-keys
az aks get-credentials --resource-group hello-app --name hello-cluster
```

The following terminates a cluster with Azure Kubernetes Service.

```
az aks delete --resource-group hello-app --name hello-cluster
az group delete --name hello-app
```

## Uploading a Confidential Container

Here are instructions for each cloud provider on uploading a confidential container using each services container registry.

### Google Kubernetes Engine

With Google, uploading and running a secure container is quite easy.

```
gcloud auth configure-docker
docker tag ${IMAGE} gcr.io/${PROJECT_ID}/hello-app
docker push gcr.io/${PROJECT_ID}/hello-app
```

### Amazon Elastic Kubernetes Service

First, create a container registry instance in the correct region through the user interface.  Then, complete the following steps.

```
aws ecr get-login --region ${REGION} --no-include-email
```

This should return a Docker command that can be used to log into the registry.  Then, do the following to push the containers.

```
docker tag ${IMAGE} ${ACCOUNT}.dkr.ecr.${REGION}.amazonaws.com/hello-app
docker push ${ACCOUNT}.dkr.ecr.${REGION}.amazonaws.com/hello-app
```

### Microsoft Azure Kubernetes Service

First, start by creating a container registry and log in.

```
export RESOURCE_GROUP=hello-app
export REGISTRY=helloapp
az acr create --resource-group ${RESOURCE_GROUP} --name ${REGISTRY} --sku Basic
az acr login --name ${REGISTRY}
az acr list --resource-group ${RESOURCE_GROUP} --query "[].{acrLoginServer:loginServer}" --output table
```

Then, tag and upload the image.

```
docker tag ${IMAGE} ${REGISTRY}.azurecr.io/hello-app:v1
docker push {$REGISTRY}.azurecr.io/hello-app:v1
```

Then, create a service principal (save the output.)

```
az ad sp create-for-rbac --skip-assignment
```

Then, get the ACR resource identifier (save the output.)

```
az acr show --resource-group ${RESOURCE_GROUP} --name ${REGISTRY} --query "id" --output tsv
```

Then, grant the service principal permission to pull from ACR.

```
az role assignment create --assignee <appId> --scope <acrId> --role acrpull
```

Now, create your cluster using the service principal.

```
az aks create --resource-group ${RESOURCE_GROUP}-app \
    --name hello-cluster \
    --node-count 1 \
    --enable-addons monitoring \
    --generate-ssh-keys \
    --service-principal <appId> \
    --client-secret <password>
```

Then, get the credentials.

```
az aks get-credentials --resource-group ${RESOURCE_GROUP} --name hello-cluster
```
