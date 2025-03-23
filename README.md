# Jenkins CI/CD Pipeline for React Dashboard

## Overview
This project sets up a CI/CD pipeline for a React dashboard using Jenkins. It includes stages for cleaning the workspace, cloning the repository, installing dependencies, performing a code quality check with SonarQube, running a dependency check, building a Docker image, logging into Docker Hub, running the image, and pushing it to Docker Hub.

## Prerequisites
- Jenkins installed and configured
- SonarQube server running on `http://localhost:9000`
- Docker installed and configured
- Node.js installed
- A valid Docker Hub account

## Pipeline Stages
1. **Clean Workspace**: Clears the workspace before starting the build.
2. **Clone Repository**: Clones the repository from GitHub.
3. **Build and Install Dependencies**: Runs `npm install` to install required dependencies.
4. **Code Quality Check**: Runs SonarQube scanner for static code analysis.
5. **Dependency Check**: Scans dependencies for known vulnerabilities.
6. **Build Image**: Creates a Docker image for the React application.
7. **Login to Docker Hub**: Authenticates with Docker Hub.
8. **Run Image**: Runs the built Docker container.
9. **Push to Docker Hub**: Pushes the built image to Docker Hub.

## Pipeline Script
```groovy
pipeline {
    agent any
    environment {
        SCANNER_HOME = tool 'SonarScanner' // Use the scanner configured in Jenkins
        DOCKER_CREDENTIALS = "docker_id"
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/EshaalB/dashboard.git'
            }
        }
        stage('Build and Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }
        stage('Code Quality Check') {
            steps {
                 bat """ "%SCANNER_HOME%/bin/sonar-scanner" ^
                 -D"sonar.projectKey=react_app" ^
                 -D"sonar.sources=." ^
                 -D"sonar.host.url=http://localhost:9000" ^
                 -D"sonar.token=sqp_88897e210b9bbe2f6c7ec3a03ad4a548a4684495"
                 """
            }
        }
        stage('Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit --nvdApiKey=328bdb0e-22e9-4182-a0cf-964b9455424c', odcInstallation: 'Db_check'
                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
            }
        }
        stage('Build Image') {
            steps {
                bat "docker build -t hassan3334/gym-app ."
            }
        }
        stage("Login to Docker"){
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS}", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    bat "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                    echo "Successfully logged in to Docker Hub"
                }
            }
        }
        stage('Run Image') {
            steps {
                echo "docker run --name my-app -p 5050:5050 gym-app"
            }
        }
        stage("Push to Docker Hub") {
            steps {
                echo 'docker push gym-app'
            }
        }
    }
}
``` 

## How to Run the Pipeline
1. Open Jenkins and create a new pipeline job.
2. Copy the above pipeline script into the pipeline configuration.
3. Set up credentials for Docker Hub in Jenkins.
4. Run the pipeline and monitor the stages.

## Author
Hassan Nfissi

