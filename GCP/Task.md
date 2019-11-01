# Deploy a cloud application with Kubernetes

You are the lead engineer of a startup that builds an application for recruiters to determine how much a given job applicant will be expected to be paid when they apply for a particular job.  The application uses a machine learning model that has been trained on a set of confidential student data and employment records.  The CEO of your company is trying to raise another round of funding for your startup and has asked you to set up a demo of the new version of your application.  A link to the application running in the cloud will be provided to each investor that will allow them to try out the application, if they should desire.  The CEO has expressed concerns around the trained model and uploading it to the cloud: they do not want the data to be inadvertently exposed or to put the company at risk.  The CEO has given you access to the companies corporate cloud account with Google Cloud Platform and asks you to deploy the application using Google Kubernetes Engine.

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

*If you need to change the name of the image, you can rebuild it using `docker build -t` with the new name. You must use your Andrew username as part of the image name.*

**3. Launch the image using the Kubernetes cluster provided to you.**

```
kubectl create deployment <deployment_name> --image=<image_you_uploaded>
```

*You must use your Andrew username as part of the deployment name.*

**4. Ensure that the container is running.**

```
kubectl get pods
```

Make sure that the container status is `Running`. If you see this message, you are done with the task. Now, please help your teammates to complete their task. If your container status is not `Running`, then:

- If you see `ImagePullBackoff`, your image could not be pulled. You may have specified the image name incorrectly. Run `kubectl delete deployment <deployment_name>` and return to step 3.
- If you see `ErrImgPull`, your image could not be accessed by your Kubernetes cluster because it does not have permissions to the image you pushed to the container registry. You may need to search how to grant access from your container registry to your Kubernetes cluster. Run `kubectl delete deployment <deployment_name>`, grant any required permissions, and return to step 3 to try again.

**5. Remove the deployment.**

```
kubectl delete service <deployment_name>
kubectl delete deployment <deployment_name>
```
