# Gym App Project

This repository contains the source code for the Gym App, a React-based application deployed via a Jenkins CI/CD pipeline. The pipeline automates the process of building, testing, and deploying the application to Docker Hub.

## Project Overview

- **Repository**: [https://github.com/EshaalB/dashboard](https://github.com/EshaalB/dashboard)
- **Docker Image**: `hassan3334/gym-app`
- **Technologies**: Node.js, React, Docker, Jenkins, SonarQube, Dependency-Check

## Jenkins Pipeline

The pipeline is defined in a `Jenkinsfile` and includes the following stages:

### 1. Clean Workspace
- **Purpose**: Removes any existing files in the Jenkins workspace to ensure a clean build.
- **Command**: `cleanWs()`

### 2. Clone the Repository
- **Purpose**: Fetches the latest code from the `main` branch of the repository.
- **URL**: [https://github.com/EshaalB/dashboard.git](https://github.com/EshaalB/dashboard.git)

### 3. Build and Install Dependencies
- **Purpose**: Installs all required Node.js dependencies for the React application.
- **Command**: `npm install`

### 4. Code Quality Check
- **Purpose**: Analyzes code quality using SonarQube Scanner.
- **Tool**: SonarScanner (configured in Jenkins as `SonarScanner`)
- **Configuration**:
  - Project Key: `react_app`
  - Source Directory: `.`
  - SonarQube URL: `http://localhost:9000`
  - Sonar Token: `sqp_88897e210b9bbe2f6c7ec3a03ad4a548a4684495`

### 5. Dependency Check
- **Purpose**: Scans for vulnerabilities in project dependencies using OWASP Dependency-Check.
- **Arguments**: `--scan ./ --disableYarnAudit --disableNodeAudit --nvdApiKey=328bdb0e-22e9-4182-a0cf-964b9455424c`
- **Installation**: `Db_check`
- **Report**: Publishes `dependency-check-report.xml`

### 6. Build Image
- **Purpose**: Builds a Docker image for the application.
- **Command**: `docker build -t hassan3334/gym-app .`

### 7. Login to Docker
- **Purpose**: Authenticates with Docker Hub using stored credentials.
- **Credentials**: `docker_id` (stored in Jenkins as `DOCKER_CREDENTIALS`)
- **Output**: Confirms successful login.

### 8. Run Image (Simulation)
- **Purpose**: Simulates running the Docker container locally.
- **Command**: `docker run --name my-app -p 5050:5050 gym-app` (echoed only)

### 9. Push to Docker Hub (Simulation)
- **Purpose**: Simulates pushing the built Docker image to Docker Hub.
- **Command**: `docker push gym-app` (echoed only)

## Pipeline Design

Below is a screenshot of the pipeline design:

![Pipeline Design](screenshots/pipeline-design.png)

*Note*: Replace `screenshots/pipeline-design.png` with the actual path to your pipeline design screenshot after adding it to the repository.

## Prerequisites

- **Jenkins**: Installed and configured with necessary plugins (e.g., Git, Docker, SonarQube Scanner, Dependency-Check).
- **Docker**: Installed on the Jenkins agent.
- **SonarQube**: Running locally on `http://localhost:9000`.
- **Node.js**: Installed on the Jenkins agent for `npm install`.

## Setup Instructions

1. Clone this repository to your local machine or Jenkins server.
2. Ensure Jenkins is configured with:
   - SonarScanner tool named `SonarScanner`.
   - Docker credentials stored as `docker_id`.
   - Dependency-Check installation named `Db_check`.
3. Add the `Jenkinsfile` to the root of the repository.
4. Create a Jenkins pipeline job and point it to this repository.
5. Run the pipeline.

## Deployment

The application is deployed as a Docker container and available on Docker Hub at `hassan3334/gym-app`.

## Contributing

Feel free to submit issues or pull requests to improve the project!

## License

This project is licensed under the MIT License.
