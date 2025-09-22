# Lab3: Continuous Integration and Delivery using Jenkins

This repository contains the implementation of a CI/CD pipeline setup using Jenkins for a Node.js application. The project is based on the provided baseline repository (https://github.com/epam-msdp/cicd-pipeline), with modifications to support multibranch pipelines, branch-specific configurations (e.g., different `logo.svg` files and ports), and manual deployment options. The main and dev branches simulate different environments, with automated builds triggered via the multibranch pipeline and manual deployments handled separately.

The setup has been successfully tested, with all stages (checkout, build, test, build Docker image, deploy) functioning as expected. Differences in `logo.svg` are visible post-deployment, and ports are dynamically assigned based on the branch (3000 for main, 3001 for dev).

## Objective

The goal is to train systems engineers in setting up a Multibranch pipeline and a manual pipeline for deploying an application. This includes:
- Creating `main` and `dev` branches in Git, which act as simulated environments.
- Configuring pipeline stages: checkout, build, test, build Docker image, and deploy.
- Implementing branch-specific changes, such as modifying `logo.svg` (in .svg format) and setting different ports (e.g., http://localhost:3000 for main, http://localhost:3001 for dev).
- Adding conditional logic in the pipeline to handle port assignments based on the branch.

## Prerequisites

- Knowledge of Git, Docker, and Jenkins.
- Understanding of the application deployment process.
- Basic scripting knowledge.
- Jenkins installed on a Linux VM (download from https://www.jenkins.io/download).
- Required Jenkins plugins: Docker Pipeline, Docker plugin, Git plugin, Groovy, NodeJS plugin, Pipeline.

## Details

The pipeline stages are: checkout → build → test → build Docker image → deploy.

- **Branch-Specific Customizations**:
    - Change `logo.svg` in each branch (e.g., different images for main and dev; differences are visible after deployment).
    - Ports: 3000 for main, 3001 for dev.
    - Changes to `logo.svg` are made directly in the respective branches.
    - Conditional logic in the Jenkinsfile handles port and image tagging based on the branch.

- **Pipelines Created**:
    - **CICD**: A multibranch pipeline that automatically detects and builds the `main` and `dev` branches.
    - **CD_deploy_manual**: A regular pipeline for manual deployments, triggered with parameters to select the environment (main/dev) and Docker image tag.

The repository was created by pulling the baseline repo (https://github.com/epam-msdp/cicd-pipeline), pushing it to this GitHub repo, and adding a `dev` branch.

## Steps to Set Up and Run

1. **Configure Global Tools**:
    - Set up Node.js version 7.8.0 in Jenkins' Global Tool Configuration.

2. **Create Pipelines**:
    - Set up a multibranch pipeline named "CICD".
    - Set up a regular pipeline named "CD_deploy_manual".

3. **Configure Git**:
    - Set up GitHub SSH credentials in Jenkins for repository access.

4. **Set Up Multibranch Scanning**:
    - Configure the multibranch pipeline to scan every 1 minute (or use webhooks if you have a public IP for better efficiency).

5. **Configure Role-Based Access**:
    - Create groups: dev, qa, devops.
    - Assign permissions:
        - DevOps: Full access to Jenkins.
        - Dev: Can run jobs.
        - QA: Can read logs but cannot run jobs.

6. **Configure CICD Pipeline Stages** (in Jenkinsfile, shared across branches):
    - **Node.js Setup**: Use the configured Node.js version from Global Tools.
    - **Build**: Run `npm install`.
    - **Test**: Run `npm test`.
    - **Build Docker Image**:
        - For main: `docker build -t nodemain:v1.0 .`
        - For dev: `docker build -t nodedev:v1.0 .`
    - **Deploy**:
        - Stop and remove any previous containers to minimize downtime.
        - For main: `docker run -d --expose 3000 -p 3000:3000 nodemain:v1.0`
        - For dev: `docker run -d --expose 3001 -p 3001:3000 nodedev:v1.0`

7. **Configure CD_deploy_manual Pipeline**:
    - This pipeline is for manual deployments to simulate 'main' or 'dev' environments (using branches as proxies).
    - Parameters:
        - `main/dev`: Specifies the target environment.
        - `Image tag`: Specifies the Docker image tag to deploy (e.g., v1.0).

## Usage

- **Automated Builds**: Push changes to `main` or `dev` branches to trigger the CICD multibranch pipeline.
- **Manual Deployment**: Run the CD_deploy_manual pipeline in Jenkins, providing the environment and image tag parameters.
- **Verification**: After deployment, access the app at the branch-specific port and check for the updated `logo.svg`.

This setup demonstrates CI/CD best practices with Jenkins, including automation, conditional logic, and role-based security.
