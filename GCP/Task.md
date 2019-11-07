# Deploy a cloud application with Kubernetes

You are the Tech Lead Engineer at a startup software company that builds a web application for (1) job placement of software engineering jobs and (2) predicts the asking salary ranges of the applicants. The application performs these tasks using a prediction model training with confidential data from past users. The CEO of the company wants to raise another round of funding, and asked you to share a demo of the application, by sharing a link, to a potential investor. The CEO emphasized that the link shared with the investor must only expose the application for the investor to interact with it, but it must not expose any implementation of the application (e.g., source code, trained model, confidential data for training, etc.). You have decided to deploy the demo to Google Cloud Platform using Google Kubernetes Engine.


## Task

The following instructions indicate the steps that need to be followed to complete the task:
1. Build the image with Docker.
2. Upload your image to an Google Container Reigstry.
3. Launch the image using the Kubernetes cluster provided to you.
4. Ensure that the container is running.
5. Remove the deployment and service.

In the following paragraphs, we describe each step in detail and, in some steps, we provide commands that you need to execute to complete that subtask. For the subtasks in which we do not specific a command, you need to find those commands online.

**1. Build the image with Docker using the provided Dockerfile.**

*Please note, your image name cannot contain capital letters or underscores. You must use your Andrew username as part of the image name.*

```
cd kubernetes-hello-world-example
docker build -t <your_image_name> . 
```

**2. Upload your image to Google Container Registry.**

You will use the command line to upload your image.  Below, you will find a link to the documentation.

- [Google Container Registry](https://cloud.google.com/container-registry/docs/quickstart) 

*If you need to change the name of the image, you can rebuild it using `docker build -t` with the new name. You must use your Andrew username as part of the image name. You must use `exemplary-terra-256719` as the <project-id>.*

**3. Launch the image using the Kubernetes cluster provided to you.**

```
kubectl create deployment hello-cluster --image=<image_you_uploaded>
```

*You must use your Andrew username as part of the deployment name.*

**4. Ensure that the container is running.**

```
kubectl get pods
```

Make sure that the container status is `Running`.  If your container status is not `Running`, then:

- If you see `ImagePullBackoff`, your image could not be pulled. You may have specified the image name incorrectly. Run `kubectl delete deployment <deployment_name>` and return to step 3.
- If you see `ErrImgPull`, your image could not be accessed by your Kubernetes cluster because it does not have permissions to the image you pushed to the container registry. You may need to search how to grant access from your container registry to your Kubernetes cluster. Run `kubectl delete deployment <deployment_name>`, grant any required permissions, and return to step 3 to try again.

**5. Remove the deployment.**

```
kubectl delete deployment <deployment_name>
```
