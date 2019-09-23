# Hello Application example

This example shows how to build and deploy a containerized Go web server
application using [Kubernetes](https://kubernetes.io).

This directory contains:

- `main.go` contains the HTTP server implementation. It responds to all HTTP
  requests with a  `Hello, world!` response.
- `Dockerfile` is used to build the Docker image for the application.

This application is available as two Docker images, which respond to requests

# Running

Here's the commands to run the application using a given image.

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