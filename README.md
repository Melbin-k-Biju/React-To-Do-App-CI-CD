# Deploying a React To-Do App Using Jenkins, Docker, AWS, and Docker Hub

This guide outlines how to deploy a React To-Do application using Jenkins for CI/CD, Docker for containerization, Docker Hub for image hosting, and AWS for hosting the application.

---

## Prerequisites

1. A working React To-Do application.
2. Docker installed on your system.
3. An account on Docker Hub.
4. Jenkins installed and configured.
5. AWS account with access to EC2 or other hosting services.
6. Basic knowledge of Docker, Jenkins, and AWS.

---

## Steps

### 1. Build and Containerize the React App

1. Build the React app:
    ```bash
    npm run build
    ```

2. Create a Dockerfile in the root of your project:
    ```dockerfile
    FROM node:16-alpine
    WORKDIR /app
    COPY build/ /app/build
    RUN npm install -g serve
    CMD ["serve", "-s", "build", "-l", "3000"]
    ```

3. Build the Docker image:
    ```bash
    docker build -t react-todo-app .
    ```

4. Test the image locally:
    ```bash
    docker run -p 3000:3000 react-todo-app
    ```

---

### 2. Push the Docker Image to Docker Hub

1. Log in to Docker Hub:
    ```bash
    docker login
    ```

2. Tag the Docker image:
    ```bash
    docker tag react-todo-app <your-dockerhub-username>/react-todo-app:latest
    ```

3. Push the image to Docker Hub:
    ```bash
    docker push <your-dockerhub-username>/react-todo-app:latest
    ```

---

### 3. Set Up Jenkins Pipeline

1. Install Jenkins and necessary plugins:
    - Docker Plugin
    - Pipeline Plugin

2. Create a Jenkinsfile in the project root:
    ```groovy
    pipeline {
        agent any

        stages {
            stage('Build') {
                steps {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }

            stage('Docker Build & Push') {
                steps {
                    sh 'docker build -t <your-dockerhub-username>/react-todo-app:latest .'
                    sh 'docker login -u <your-dockerhub-username> -p <your-dockerhub-password>'
                    sh 'docker push <your-dockerhub-username>/react-todo-app:latest'
                }
            }
        }
    }
    ```

3. Configure Jenkins:
    - Create a new pipeline project.
    - Point it to your repository containing the Jenkinsfile.

4. Run the pipeline to build and push the Docker image.

---

### 4. Deploy on AWS

1. Launch an EC2 instance:
    - Use an AMI with Docker pre-installed or install Docker manually.

2. SSH into the EC2 instance and pull the Docker image:
    ```bash
    docker pull <your-dockerhub-username>/react-todo-app:latest
    ```

3. Run the Docker container:
    ```bash
    docker run -d -p 80:3000 --name react-todo-app <your-dockerhub-username>/react-todo-app:latest
    ```

4. Access the application via the public IP of the EC2 instance.

---

### 5. Automate Deployment (Optional)

- Use Jenkins to SSH into the EC2 instance and deploy the updated container automatically after building and pushing the image.

---

## Repository Structure

```
.
├── build/               # Compiled React app files
├── src/                 # React source code
├── Dockerfile           # Dockerfile for containerizing the app
├── Jenkinsfile          # Jenkins pipeline configuration
└── README.md            # This file
```

---

## Troubleshooting

- **Pipeline errors**: Check Jenkins logs for detailed error messages.
- **Container not running**: Verify Docker logs on the AWS instance:
    ```bash
    docker logs react-todo-app
    ```
- **Access issues**: Ensure the EC2 instance's security group allows traffic on port 80.

---

## License

This project is licensed under the MIT License. See the `LICENSE` file for more details.
