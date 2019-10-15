# Deploy a container in GCP

The goal of this task is to deploy a container in GCP. 

This directory contains:

- `main.go` contains the HTTP server implementation. It responds to all HTTP
  requests with a  `Hello, world!` response.
- `Dockerfile` is used to build the Docker image for the application. This file will copy all contents of this directory in the image.
- `credentials.txt` contains your GCP username and password.

The following instructions indicate the steps that need to be followed to complete the task:
1. Build the image with Docker.
2. Upload your image to an online registry provider.
3. Create a Google Kubernetes Engine.
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

You will use the command line to upload your image.

**3. Create a Google Kubernetes Engine.**

```
gcloud config set project $PROJECT_ID
gcloud config set compute/zone us-east1-b
gcloud container clusters create hello-cluster --num-nodes=2
gcloud container clusters get-credentials hello-cluster
```

TODO: WHAT IS `$PROJECT_ID`

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
gcloud container clusters delete hello-cluster
```
