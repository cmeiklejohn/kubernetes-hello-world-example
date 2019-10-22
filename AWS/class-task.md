# Deploy a container in AWS

The goal of this task is to run a container in AWS.

## Context

You are the lead engineer on the graduate admissions system. A version of the system has been extended with a model to predict whether a student should be admitted or not. The model has been trained on confidential student records and information. Your manager at CMU has asked you 
to deploy this version of the application for the 2020 REU program. You have decided to deploy this application to Amazon Web Services using Amazon Elastic Kubernetes Service.

This directory contains a `Dockerfile` to build the graduate admissions system source code. 

## Task

The following instructions indicate the steps that need to be followed to complete this task:

0. Pull and run the task container.
1. Build the image with Docker.
2. Upload your image to an online container image registry.
3. Launch the image using the Kubernetes cluster provided to you.
4. Ensure that the container is running.

In the following paragraphs, we describe each step in detail and, in some steps, we provide commands that you need to execute to complete that subtask. For the subtasks in which we do not specific a command, you need to find those commands online.

**0. Pull and run the task container.**

- Go to GitHub.com. 
- Click your user profile (on the top-right corner).
- Click settings.
- Click developer settings.
- Click personal access tokens.
- Click generate new token.
- Grant permision `repo`, `write:packages`, `read:packages`, and `delete:packages`.
- The output will be the `TOKEN` that you will use in the next command.

```
docker login docker.pkg.github.com -u <your_github_username> -p TOKEN
docker pull docker.pkg.github.com/cmu-313/kubernetes/aws:latest
docker run -v /var/run/docker.sock:/var/run/docker.sock --privileged -it docker.pkg.github.com/cmu-313/kubernetes/aws:latest
```

**1. Build the image with Docker.**

*Please note, your image name cannot contain capital letters or underscores. You must use your Andrew username as part of the image name.*

```
cd code
docker build -t <image_name> . 
```

**2. Upload your image to an online container image registry.**

You will use the command line to upload your image. 

You have the choice of uploading your image using either of the following.  It may be useful to view the documentation by using the following links prior to making your choice.

- Docker Hub (https://docs.docker.com/engine/reference/commandline/push/) 
- Amazon Elastic Container Registry (https://docs.aws.amazon.com/AmazonECR/latest/userguide/Registries.html)

Regardless of which choice you make to you store your image, you will be able to run the container with Amazon Elastic Kubernetes Service.
*If you need to change the name of the image, you can rebuild it using `docker build -t` with the new name. You must use your Andrew username as part of the image name.*

- If you choose Docker Hub, you'll be using 313cmu's credentials, so your image will have to be named `313cmu/<image_name>`.   You are already logged into Docker in the VM that was provided to you.
- If you choose ECR, you'll need to create a container registry and push your image to it using the Amazon CLI.  You are already logged into ECR in the VM that was provided to you using 313cmu's account.

**3. Launch the image using the Kubernetes cluster provided to you.**

*You must use your Andrew username as part of the deployment name. `pushed_image_name` is the name you used to `docker push` your image.*

```
kubectl create deployment <deployment_name> --image=<pushed_image_name>
```

**4. Ensure that the container is running.**

```
kubectl get pods
```

Make sure that the container status is `Running`. If you see this message, you are done with the task. Now, please help your teammates to complete their task. If your container status is not `Running`, then:

- If you see `ImagePullBackoff`, your image could not be pulled. You may have specified the image name incorrectly. Run `kubectl delete deployment <deployment_name>` and return to step 3.
- If you see `ErrImgPull`, your image could not be accessed by your Kubernetes cluster because it does not have permissions to the image you pushed to the container registry. You may need to search how to grant access from your container registry to your Kubernetes cluster. Run `kubectl delete deployment <deployment_name>`, grant any required permissions, and return to step 3 to try again.
