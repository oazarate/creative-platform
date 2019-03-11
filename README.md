# Creative Platform Using Public AI Models Through Postman

_Note: This repo draws material directly from this [Medium article](https://medium.com/analytics-vidhya/deploy-your-first-deep-learning-model-on-kubernetes-with-python-keras-flask-and-docker-575dc07d9e76) and the [IBM](https://github.com/IBM) MAX model documentation._

_These APIs will only be running from March 11th, 2019 to March 22nd, 2019, or until compute credits are depleted._

## Using the Platform

To use the platform:

* Download and install [Postman](https://www.getpostman.com/)
* Download [../creative-platform.postman_collection.json](creative-platform.postman_collection.json) and open it in Postman.
![Instructions-1](images/instructions-1.png?raw=true)
* On the left, select the app you want to use, e.g. style-transfer-mosaic
![Instructions-2](images/instructions-2.png?raw=true)
* Go to body
* Next to image, click and select the image you wish to submit to the app
* Click Send
![Instructions-3](images/instructions-3.png?raw=true)

To run your own implementation of the models on Google Cloud Platform's Kubernetes Engine, follow the steps below.

## Creating the Platform

### Accounts

If you do not have accounts with these services, create them. You will need compute credits for Google Cloud Platform.

* [Google Cloud Platform account](https://cloud.google.com/)
* [Docker Hub account](https://hub.docker.com/)

### Creating the APIs

If you wish to create an API for use with the platform, you can use [this framework](https://medium.com/analytics-vidhya/deploy-your-first-deep-learning-model-on-kubernetes-with-python-keras-flask-and-docker-575dc07d9e76) to transform your model into an API, though the author notes it may not be ideal for production.

The framework mentioned above was used the create the Docker image of the Image Net Classifier.
From here on out, we will assume that any Docker images of interest are already APIs and available on DockerHub or GitHub.

### Start your Kubernetes cluster

Open your Google Cloud Platform Console and go to Kubernetes Engine.

Create a cluster, selecting 2 nodes and 4vCPUs with 15 GBs of RAM.

Open the cloud shell from the console.

### Obtaining the Docker Images of Interest

We will be running the following models in our platform:

* [Image Net Classifier](https://medium.com/analytics-vidhya/deploy-your-first-deep-learning-model-on-kubernetes-with-python-keras-flask-and-docker-575dc07d9e76) As implemented by Gus Cavanaugh and Adrian Rosebrock

* [IBM MAX-Fast-Neural-Style-Transfer](https://github.com/IBM/MAX-Fast-Neural-Style-Transfer)

To deploy the Image Net classifier, run

```
kubectl run keras-app --image=oazarate/keras-app --port 5000
```

Then check the status with `kubectl get pods` until it returns READY

```
kubectl get pods
```

Once it is ready, expose the port to make it publicly available

```
kubectl expose deployment keras-app --type=LoadBalancer --port 80 --target-port 5000
```

Then run `kubectl get service` to find the external IP.

This IP directs to the app and is later used in the creation of the json object for Postman.

To use the IBM MAX models, use:

```
kubectl apply -f https://raw.githubusercontent.com/IBM/MAX-Fast-Neural-Style-Transfer/master/max-fast-neural-style-transfer.yaml

```

However, as before, the app is only available locally. In this case, we create a service that exposes it

```
kubectl expose deployment max-fast-neural-style-transfer --type=LoadBalancer --name=my-service
```

You can the find the external IP of the service with `kubectl get service`
