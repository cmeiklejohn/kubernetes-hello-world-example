You are the lead engineer of a startup that builds an application for recruiters to determine the salary a given job applicant will be expected to be paid when they apply for a particular job.  

The application uses a machine learning model:

* This model has been trained with confidential student data.
* This model is considered intellectual property of the company.

The CEO of your company is trying to raise another round of funding for your startup and has asked you to set up a demo of the new version of your application.  The CEO would like to provide a link to the new version of the application running in the Cloud to each potential investor in order to try out the application before it launches, if they want to.  The CEO has expressed concerns around the machine learning model being use in the cloud environment: they do not wish to potentially expose any information that should remain confidential nor put the company at risk by uploading this to the cloud.  

You have the application code containing the trained model exposed as a REST service with authentication and a Dockerfile for building a Docker container containing the application code.  The CEO has given you access to the company's corporate Cloud account with Amazon Web Services and has asked you to deploy the application using their existing Amazon Elastic Kubernetes Service cluster.

For this task, you'll need to do the following:  

1. Build the image with Docker.
2. Upload your image to an Amazon Container Reigstry.
3. Launch the image using the Kubernetes cluster provided to you.
4. Ensure that the container is running.

To assist you in this task, we've provided you simplified instructions for running the container using Kubernetes that deploys a single instance of the application, enough for the required demo you'll make available to the potential investors.

## Task

The following instructions indicate the steps that need to be followed to complete the task:
1. Build the image with Docker.
2. Upload your image to an Amazon Container Registry.
3. Launch the image using the Kubernetes cluster provided to you.
4. Ensure that the container is running.
5. Remove the deployment and service.

In the following paragraphs, we describe each step in detail and, in some steps, we provide commands that you need to execute to complete that subtask. For the subtasks in which we do not specific a command, you need to find those commands online.

**1. Build the image with Docker using the provided Dockerfile.**

*Please note, your image name cannot contain capital letters or underscores.*

```
cd kubernetes-hello-world-example
docker build -t <your_image_name> . 
```

**2. Upload your image to Amazon Container Registry.**

You will use the command line to upload your image.  Below, you will find a link to the documentation.

- [Amazon Container Registry](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html) 

*If you need to change the name of the image, you can rebuild it using `docker build -t` with the new name.*

**3. Launch the image using the Kubernetes cluster provided to you.**

```
kubectl create deployment hello-cluster --image=<image_you_uploaded>
```

**4. Ensure that the container is running.**

```
kubectl get pods
```

Make sure that the container status is `Running`.  If your container status is not `Running`, then:

- If you see `ImagePullBackoff`, your image could not be pulled. You may have specified the image name incorrectly. Run `kubectl delete deployment <deployment_name>` and return to step 3.
- If you see `ErrImgPull`, your image could not be accessed by your Kubernetes cluster because it does not have permissions to the image you pushed to the container registry. You may need to search how to grant access from your container registry to your Kubernetes cluster. Run `kubectl delete deployment <deployment_name>`, grant any required permissions, and return to step 3 to try again.

If your container is `Running`, you've successfully deployed your application.

**5. Remove the deployment.**

```
kubectl delete deployment hello-cluster
```
