[![CircleCI](https://dl.circleci.com/status-badge/img/gh/jorgeatcabo/MachineLearningMicroserviceAPI/tree/main.svg?style=svg)](https://dl.circleci.com/status-badge/redirect/gh/jorgeatcabo/MachineLearningMicroserviceAPI/tree/main)

## Project Overview

This project operationalizes a Machine Learning Microservice API. 

This project is given with a pre-trained, `sklearn` model that has been trained to predict housing prices in Boston according to several features, such as average rooms in a home and data about highway access, teacher-to-pupil ratios, and so on. You can read more about the data, which was initially taken from Kaggle, on [the data source site](https://www.kaggle.com/c/boston-housing). This project allows you to operationalize a Python flask app—in a provided file, `app.py`—that serves out predictions (inference) about housing prices through API calls. This project could be extended to any pre-trained machine learning model, such as those for image recognition and data labeling.

### Project Tasks

This project operationalizes a machine learning microservice using [kubernetes](https://kubernetes.io/), which is an open-source system for automating the management of containerized applications. This project allows you to:
* Test the project code using linting
* Complete a Dockerfile to containerize this application
* Deploy the containerized application using Docker and make a prediction
* Logging
* Configure Kubernetes and create a Kubernetes cluster
* Deploy a container using Kubernetes and make a prediction
* Upload a complete Github repo with CircleCI to indicate that your code has been tested

---
### The Instructions Below Are for AWS Cloud9 Environment

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
