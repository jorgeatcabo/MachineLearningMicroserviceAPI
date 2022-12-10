[![CircleCI](https://dl.circleci.com/status-badge/img/gh/jorgeatcabo/MachineLearningMicroserviceAPI/tree/main.svg?style=svg)](https://dl.circleci.com/status-badge/redirect/gh/jorgeatcabo/MachineLearningMicroserviceAPI/tree/main)

## Project Overview

This project operationalizes a Machine Learning Microservice API. 

This project is given with a pre-trained, `sklearn` model that has been trained to predict housing prices in Boston according to several features, such as average rooms in a home and data about highway access, teacher-to-pupil ratios, and so on. You can read more about the data, which was initially taken from Kaggle, on [the data source site](https://www.kaggle.com/c/boston-housing). This project allows you to operationalize a Python flask app—in a provided file, `app.py`—that serves out predictions (inference) about housing prices through API calls. This project could be extended to any pre-trained machine learning model, such as those for image recognition and data labeling.

## Project Objectives

This project operationalizes a machine learning microservice using [kubernetes](https://kubernetes.io/), which is an open-source system for automating the management of containerized applications. This project allows you to:
* Test the project code using linting
* Complete a Dockerfile to containerize this application
* Deploy the containerized application using Docker and make a prediction
* Logging
* Configure Kubernetes and create a Kubernetes cluster
* Deploy a container using Kubernetes and make a prediction
* Upload a complete Github repo with CircleCI to indicate that your code has been tested

---
## The Instructions Below Are for AWS Cloud9 Environment

## Clone the Project
```bash
git clone git@github.com:jorgeatcabo/MachineLearningMicroserviceAPI.git
```

## Libraries to install
* Hadolint:
```bash
sudo wget -O /bin/hadolint https://github.com/hadolint/hadolint/releases/download/v2.12.0/hadolint-Linux-x86_64
sudo chmod +x /bin/hadolint
```

* Kubernetes(Minikube):
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```


## Setup the Environment

* Create a virtualenv with Python 3.7 and activate it. Refer to this link for help on specifying the Python version in the virtualenv. 
```bash
python3 -m venv ~/.devops
source ~/.devops/bin/activate
```
* Run `make install` to install the necessary dependencies

## Run Lint Checks
This project also must pass two lint checks; hadolint checks the Dockerfile for errors and pylint checks the app.py source code for errors.
In your terminal, type: make lint to run lint checks on the project code. If you haven’t changed any code, all requirements should be satisfied, and you should see a printed statement that rates your code (and prints out any additional comments):
```bash
------------------------------------
Your code has been rated at 10.00/10
```

## Dockerfile
The Dockerfile contains all the commands a user could call on the command line to assemble an image.
 * The `FROM` instruction initializes a new build stage and sets the base image for subsequent instructions. In this case, it specifies Python3 as the base image for this application.
 * `WORKDIR /app` specify a working directory.
 * `COPY . app.py /app/` Copy the `app.py` source code to that directory.
 * Install any dependencies in requirements.txt.
 ```bash
  RUN pip install --no-cache-dir --upgrade pip &&\
    pip install --no-cache-dir --trusted-host pypi.python.org -r requirements.txt
 ```
After you complete this file and save it, it is recommended that you go back to your terminal and run `make lint` again to see if `hadolint` catches any errors in your Dockerfile.

## Run a Container & Make a Prediction
In order to run a containerized application, you’ll need to build and run the docker image that you defined in the `Dockerfile`, and then you should be able to test your application, locally, by having the containerized application accept some input data and produce a prediction about housing prices. `run_docker.sh`
The `run_docker.sh` file contains all the commands to be able to get Docker running, locally.
* `docker build --tag=app .` Build the docker image from the Dockerfile.
* `docker image ls` List the created docker images (for logging purposes).
* `docker run -p 8000:80 app` Run the containerized Flask app; publish the container’s port to a host port. The appropriate container and host ports are in the Dockerfile and `make_prediction.sh` files, respectively.

## Running the complete script
To run and build a docker image, you’ll type `./run_docker.sh`.
After a brief waiting period, you should see messages indicating a successful build, along with some indications that your app is being served on port 80 (also, a warning about the development server is to be expected, here).
```bash
Successfully built <build id>
Successfully tagged <your tag>
```
This indicates a successful build and if you keep this application running you can make predictions!

## Making predictions
Then, to make a prediction, you have to open a separate tab or terminal window. In this new window, navigate to the main project directory (some computers will do this automatically) and call `./make_prediction.sh`.
This shell script is responsible for sending some input data to your containerized application via the appropriate port. Each numerical value in here represents some feature that is important for determining the price of a house in Boston. The source code is responsible for passing that data through a trained, machine learning model, and giving back a predicted value for the house price.

In the prediction window, you should see the value of the prediction, and in your main window, where it indicates that your application is running, you should see some log statements print out. You’ll see that it prints out the input payload at multiple steps; when it is JSON and when it’s been converted to a DataFrame and about to be scaled.

After making a prediction, you can type CTRL+C (+enter) to quit running your application. You can always re-run it with a call to `./run_docker.sh`.

## Upload the Docker Image
Now that you’ve tested your containerized image locally, you’ll want to upload your built image to docker. This will make it accessible to a Kubernets cluster.
The `upload_docker.sh` file contains the commands for uploading the Docekr image:
* `dockerpath=601360/app` Define a `dockerpath` which will be “/path”; the path may be the same as the build tag you created in `run_docker.sh` or just some descriptive path name. Recall that your docker username is your unique docker ID.
* Authenticate and tag image; this step is responsible for creating a login step and ensuring that the uploaded docker image is tagged descriptively.
```bash
docker login --username 601360
docker tag app $dockerpath
```
* Similar to how you might push a change to a Github repository, push your docker image to the `dockerpath`. This push may take a moment to complete.
```bash
docker push $dockerpath
```

Assuming you’ve already built the docker image with `./run_docker.sh`, you can now upload the image by calling the complete shell script `./upload_docker.sh`.

If you’ve successfully implemented authentication and tagging, you should see a successful login statement and a repository name that you specified, printed in your terminal. You should also be able to see your image as a repository in your docker hub account.

## Configure Kubernetes to Run Locally
To start a local cluster, type the terminal command: `minikube start`.
After minikube starts, a cluster should be running locally. You can check that you have one cluster running by typing `kubectl config view` where you should see at least one cluster with a `certificate-authority` and `server`.

## Deploy with Kubernetes
Now that you’ve uploaded a docker image and configured Kubernetes so that a cluster is running, you’ll be able to deploy your application on the Kubernetes cluster. This involves running your containerized application using `kubectl`, which is a command line interface for interacting with Kubernetes clusters.
The `run_kubernetes.sh` contains the commands for deploying this application using `kubectl`.
The steps will be somewhat similar to what you did in both `run_docker.sh` and `upload_docker.sh` but specific to kubernetes clusters:
* `dockerpath=601360/app` Define a dockerpath which will be “/path”, this should be the same name as your uploaded repository (the same as in `upload_docker.sh`).
* `kubectl run app --image=$dockerpath --port=80` Run the docker container with `kubectl`; you’ll have to specify the container and the port.
* `kubectl get pods` List the kubernetes pods.
* `kubectl port-forward app 8000:80` Forward the container port to a host port, using the same ports as before.
* 
call the script `./run_kubernetes.sh`. This assumes you have a local cluster configured and running. This script should create a pod with a name you specify and you may get an initial output with a cluster and status.

Initially, your pod may be in the process of being created, as indicated by `STATUS: ContainerCreating`, but you just have to wait a few minutes until the pod is ready, then you can run the script again.

Waiting: You can check on your pod’s status with a call to `kubectl get pod` and you should see the status change to `Running`. Then you can run the full `./run_kuberenets.sh` script again.

## Make a prediction
After you’ve called `run_kubernetes.sh`, and a pod is up and running, make a prediction using a separate terminal tab, and a call to `./make_prediction.sh`, as you did before.

### Running `app.py`

1. Standalone:  `python app.py`
2. Run in Docker:  `./run_docker.sh`
3. Run in Kubernetes:  `./run_kubernetes.sh`

### Kubernetes Steps

* Setup and Configure Docker locally
* Setup and Configure Kubernetes locally
* Create Flask app in Container
* Run via kubectl

### Troubleshooting
* In general, you can verify installation by checking the version of a library, ex. `kubectl version`  or `docker --version`. If there is no package found, you may need to install that library.
