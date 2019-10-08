# Container Maintenance

Here are instructions on uploading and operating the container securely with each cloud provider's environment.

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
aws ecr get-login --region ${REGION}--no-include-email
```

This should return a Docker command that can be used to log into the registry.  Then, do the following to push the containers.

```
docker tag ${IMAGE} ${ACCOUNT}.dkr.ecr.${REGION}.amazonaws.com/hello-app
docker push ${ACCOUNT}.dkr.ecr.${REGION}.amazonaws.com/hello-app
```

Next, create a secret in Kubernetes using a Docker authorization token.

```
export TOKEN=`aws ecr --region=$REGION get-authorization-token --output text --query authorizationData[].authorizationToken | base64 -d | cut -d: -f2`
export SECRET_NAME=${REGION}-ecr-registry
export EMAIL=email@email.com
kubectl delete secret --ignore-not-found $SECRET_NAME
kubectl create secret docker-registry $SECRET_NAME \
 --docker-server=https://${ACCOUNT}.dkr.ecr.${REGION}.amazonaws.com \
 --docker-username=AWS \
 --docker-password="${TOKEN}" \
 --docker-email="${EMAIL}"
```

Then, update the secret you created in the Kubernetes deployment configuration.

### Microsoft Azure Kubernetes Service