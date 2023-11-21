# Cloud Native Resource Monitoring Python App

This is a Python application for monitoring resource usage in a cloud-native environment, such as Kubernetes. The app is built using Flask and psutil, and it can be run locally or containerized with Docker.

![CPU Utilization Screenshot](https://github.com/ashwaq06/Cloud-native-monitoring-app/assets/80192952/12442fb4-7dc3-4ea4-b626-c735e7b1d05f)
![Memory Utilization Screenshot](https://github.com/ashwaq06/Cloud-native-monitoring-app/assets/80192952/0492888d-977f-4b62-97d3-05e302f64077)



## 🌟 Features

- Monitor CPU, memory, disk, and network usage of your Kubernetes nodes
- Visualize resource usage using interactive charts and graphs
- Easily deploy the application using Kubernetes Deployments and Services
- Containerized using Docker and pushed to AWS ECR for easy deployment on Kubernetes

## 🔑 Requirements

- Python3
- Flask
- psutil
- AWS
- Docker
- Kubernetes

## 🚀 Getting Started

### Part 1: Deploying the Flask application locally

**Step 1: Clone the code**

Clone the code from the repository:

```
$ git clone https://github.com/ashwaq06/Cloud-native-monitoring-app
```
**Step 2: Install dependencies**

The application uses the psutil, Flask, Plotly, and boto3 libraries. Install them using pip:

```shell
pip3 install -r requirements.txt
```
**Step 3: Run the application**

To run the application, navigate to the root directory of the project and execute the following command:

```bash
$ python3 app.py
```
This will start the Flask server on localhost:5000. Navigate to http://localhost:5000/ on your browser to access the application.

### Part 2: Dockerizing the Flask application
**Step 1: Create a Dockerfile**

Create a Dockerfile in the root directory of the project with the following contents:
```bash
# Use the official Python image as the base image
FROM python:3.9-slim-buster

# Set the working directory in the container
WORKDIR /app

# Copy the requirements file to the working directory
COPY requirements.txt .

# Install dependencies
RUN pip3 install --no-cache-dir -r requirements.txt

# Copy the application code to the working directory
COPY . .

# Set the environment variables for the Flask app
ENV FLASK_RUN_HOST=0.0.0.0

# Expose the port on which the Flask app will run
EXPOSE 5000

# Start the Flask app when the container is run
CMD ["flask", "run"]
```
**Step 2: Build the Docker image**

To build the Docker image, execute the following command:
```bash
$ docker build -t <image_name> .
```
**Step 3: Run the Docker container**

To run the Docker container, execute the following command:

```bash
$ docker run -p 5000:5000 <image_name>
```
This will start the Flask server in a Docker container on localhost:5000. Navigate to http://localhost:5000/ on your browser to access the application.

![Docker Container Screenshot](https://github.com/ashwaq06/Cloud-native-monitoring-app/assets/80192952/a271f2ad-409d-409a-85eb-f326ecf78e61)

### Part 3: Pushing the Docker image to ECR 
**Step 1: Create an ECR repository**

Create an ECR repository using Python:

```py
import boto3

# Create an ECR client
ecr_client = boto3.client('ecr')

# Create a new ECR repository
repository_name = 'my-ecr-repo'
response = ecr_client.create_repository(repositoryName=repository_name)

# Print the repository URI
repository_uri = response['repository']['repositoryUri']
print(repository_uri)
```
**Step 2: Push the Docker image to ECR**

Push the Docker image to ECR using the push command on the console:

```
$ docker push <ecr_repo_uri>:
```
### Part 4: Creating an EKS cluster and deploying the app using Python
**Step 1: Create an EKS cluster**

Create an EKS cluster and add node group.

**Step 2: Create a node group**

Create a node group in the EKS cluster.

**Step 3: Create deployment and service**
```jsx
from kubernetes import client, config

# Load Kubernetes configuration
config.load_kube_config()

# Create a Kubernetes API client
api_client = client.ApiClient()

# Define the deployment
deployment = client.V1Deployment(
    metadata=client.V1ObjectMeta(name="my-flask-app"),
    spec=client.V1DeploymentSpec(
        replicas=1,
        selector=client.V1LabelSelector(
            match_labels={"app": "my-flask-app"}
        ),
        template=client.V1PodTemplateSpec(
            metadata=client.V1ObjectMeta(
                labels={"app": "my-flask-app"}
            ),
            spec=client.V1PodSpec(
                containers=[
                    client.V1Container(
                        name="my-flask-container",
                        image="568373317874.dkr.ecr.us-east-1.amazonaws.com/my-cloud-native-repo:latest",
                        ports=[client.V1ContainerPort(container_port=5000)]
                    )
                ]
            )
        )
    )
)

# Create the deployment
api_instance = client.AppsV1Api(api_client)
api_instance.create_namespaced_deployment(
    namespace="default",
    body=deployment
)

# Define the service
service = client.V1Service(
    metadata=client.V1ObjectMeta(name="my-flask-service"),
    spec=client.V1ServiceSpec(
        selector={"app": "my-flask-app"},
        ports=[client.V1ServicePort(port=5000)]
    )
)

# Create the service
api_instance = client.CoreV1Api(api_client)
api_instance.create_namespaced_service(
    namespace="default",
    body=service
)
```
Make sure to modify the image name on line 25 with the URI of your image.
- After modifying the file, run the command "python3 eks.py" to create the deployment and service.
- To verify that the deployment and service have been created successfully, use the following commands:

To check the deployments
``` jsx
kubectl get deployment -n default
```
To check the service
```jsx
kubectl get service -n default
``` 
To check the pods
```jsx
kubectl get pods -n default
``` 

- Once the pod is up and running, you can expose the service using port-forwarding. To do this, run the following command:

```jsx
kubectl port-forward service/<service_name> 5000:5000
```
This will allow you to access the service on your local machine at http://localhost:5000.
