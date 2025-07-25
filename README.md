# Flask App with CI/CD Pipeline and Terraform Infrastructure

This project demonstrates a complete CI/CD (Continuous Integration/Continuous Deployment) pipeline for a simple "Hello World" Flask application. The pipeline is managed by Jenkins, the application is containerized using Docker, and the infrastructure on AWS is provisioned using Terraform.

## Project Structure

Here is an overview of the key files in this project:

* `app.py`: The main Python file for the Flask application. It defines a single route that returns a "Hello World" message.
* `requirements.txt`: Contains the Python dependencies for the project (just Flask).
* `Dockerfile`: A script to build a Docker image for the Flask application.
* `Jenkinsfile`: Defines the Jenkins pipeline, which automates the process of building, testing, and deploying the application.
* `main.tf`: A Terraform script to provision the necessary AWS infrastructure (an EC2 instance and a security group).

## Prerequisites

Before you begin, make sure you have the following tools and accounts set up:

* **Git:** For version control.
* **Docker and Docker Hub:** To build and store the application's Docker image.
* **Jenkins:** To run the CI/CD pipeline.
* **AWS Account:** To host the application on an EC2 instance.
* **Terraform:** To provision the AWS infrastructure.

## Getting Started

Follow these steps to get the application up and running:

### Step 1: Clone the Repository

Clone this repository to your local machine:

```bash
git clone <your-repository-url>
cd <your-repository-name>
```

### Step 2: Build and Push the Docker Image

1.  **Build the Docker image:**
    ```bash
    docker build -t hello-world-app .
    ```

2.  **Tag the image with your Docker Hub username:**
    ```bash
    docker tag hello-world-app your_docker_hub_username/hello-world-app:latest
    ```

3.  **Push the image to Docker Hub:**
    ```bash
    docker push your_docker_hub_username/hello-world-app:latest
    ```
    *(Remember to replace `your_docker_hub_username` with your actual Docker Hub username.)*

### Step 3: Set Up the Jenkins Pipeline

1.  In your Jenkins dashboard, create a new "Pipeline" project.
2.  In the project configuration, under the "Pipeline" section, select "Pipeline script from SCM".
3.  Select "Git" as the SCM and enter the URL of your repository.
4.  The "Script Path" should be `Jenkinsfile` (this is the default).
5.  Save the project. Jenkins will now automatically run the pipeline whenever you push changes to the repository.

### Step 4: Provision Infrastructure with Terraform

The `main.tf` file will provision an EC2 instance and a security group on AWS.

1.  **Initialize Terraform:**
    ```bash
    terraform init
    ```

2.  **Update the `main.tf` file:**
    * Open the `main.tf` file and replace `your_docker_hub_username` with your Docker Hub username in the `user_data` script.

3.  **Apply the Terraform configuration:**
    ```bash
    terraform apply
    ```
    Terraform will show you a plan and ask for confirmation. Type `yes` to proceed.

    This will create:
    * An EC2 instance with a user data script that installs Docker and runs your application container.
    * A security group that allows inbound traffic on port 22 (for SSH) and port 5000 (for the Flask app).
    * An SSH key pair for accessing the instance, with the private key saved as `deployer-key.pem`.

## Accessing the Application

Once the `terraform apply` command is complete, it will output the public IP address of the EC2 instance. You can access your Flask application by navigating to the following URL in your web browser:

`http://<public_ip_address>:5000`

## CI/CD Pipeline Overview

The `Jenkinsfile` defines the following stages:

1.  **Clone Repository:** Clones the source code from the Git repository.
2.  **Build Docker Image:** Builds a new Docker image from the `Dockerfile`.
3.  **Stop and Remove Existing Container:** Stops and removes any existing container with the same name to avoid conflicts.
4.  **Run New Container:** Runs a new container from the newly built Docker image.
5.  **Health Check:** Performs a simple health check to ensure the application is running.

This pipeline ensures that every change pushed to the repository is automatically built, tested, and deployed, providing a seamless and automated workflow.
