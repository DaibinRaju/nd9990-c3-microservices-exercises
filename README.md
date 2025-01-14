# Udagram Image Filtering Application
## Refactor monolith to microservice ad deploy project for udacity cloud developer nanodegree

Udagram is a simple cloud application developed alongside the Udacity Cloud Engineering Nanodegree. It allows users to register and log into a web client, post photos to the feed, and process photos using an image filtering microservice.

The project is split into two parts:
1. Frontend - Angular web application built with Ionic Framework
2. Backend RESTful API - Node-Express application

## Getting Started
> _tip_: it's recommended that you start with getting the backend API running since the frontend web application depends on the API.

### Prerequisite
1. The depends on the Node Package Manager (NPM). You will need to download and install Node from [https://nodejs.com/en/download](https://nodejs.org/en/download/). This will allow you to be able to run `npm` commands.
2. Environment variables will need to be set. These environment variables include database connection details that should not be hard-coded into the application code.

#### Environment Script
A file named `set_env.sh` has been prepared as an optional tool to help you configure these variables on your local development environment.
 
We do _not_ want your credentials to be stored in git. After pulling this `starter` project, run the following command to tell git to stop tracking the script in git but keep it stored locally. This way, you can use the script for your convenience and reduce risk of exposing your credentials.
`git rm --cached set_env.sh`

Afterwards, we can prevent the file from being included in your solution by adding the file to our `.gitignore` file.

### 1. Database
Create a PostgreSQL database either locally or on AWS RDS. The database is used to store the application's metadata.

* We will need to use password authentication for this project. This means that a username and password is needed to authenticate and access the database.
* The port number will need to be set as `5432`. This is the typical port that is used by PostgreSQL so it is usually set to this port by default.

Once your database is set up, set the config values for environment variables prefixed with `POSTGRES_` in `set_env.sh`.
* If you set up a local database, your `POSTGRES_HOST` is most likely `localhost`
* If you set up an RDS database, your `POSTGRES_HOST` is most likely in the following format: `***.****.us-west-1.rds.amazonaws.com`. You can find this value in the AWS console's RDS dashboard.


### 2. S3
Create an AWS S3 bucket. The S3 bucket is used to store images that are displayed in Udagram.

Set the config values for environment variables prefixed with `AWS_` in `set_env.sh`.

### 3. Backend API
Launch the backend API locally. The API is the application's interface to S3 and the database.

* To download all the package dependencies, run the command from the directory `udagram-api/`:
    ```bash
    npm install .
    ```
* To run the application locally, run:
    ```bash
    npm run dev
    ```
* You can visit `http://localhost:8080/api/v0/feed` in your web browser to verify that the application is running. You should see a JSON payload. Feel free to play around with Postman to test the API's.

### 4. Frontend App
Launch the frontend app locally.

* To download all the package dependencies, run the command from the directory `udagram-frontend/`:
    ```bash
    npm install .
    ```
* Install Ionic Framework's Command Line tools for us to build and run the application:
    ```bash
    npm install -g ionic
    ```
* Prepare your application by compiling them into static files.
    ```bash
    ionic build
    ```
* Run the application locally using files created from the `ionic build` command.
    ```bash
    ionic serve
    ```
* You can visit `http://localhost:8100` in your web browser to verify that the application is running. You should see a web interface.

## Deployment to AWS
Create accounts in Travis CI and docker hub
Create 4 repos in dockerhub for 4 docker images and edit their names in .travis.yml file
Add required env var in travis website
connect github to travis and pulls to the repo trigger travis 
After successful build you can see 4 images in docker hub

Edit required files in the deployments folder.
You can check the format of aws credentials by decoding base64 value in the aws-secret.yaml file

Create custer in aws and connect eks to kubectl
While creating node groups make sure you use t3.medium or abouve with enough memory

run the following commands
```bash
    # Apply env variables and secrets
    kubectl apply -f aws-secret.yaml
    kubectl apply -f env-secret.yaml
    kubectl apply -f env-configmap.yaml
    # Deployments - Double check the Dockerhub image name and version in the deployment files
    kubectl apply -f backend-feed-deployment.yaml
    # Do the same for other three deployment files
    # Service
    kubectl apply -f backend-feed-service.yaml
    # Do the same for other three service files
```

Expose external IP
```bash
    kubectl expose deployment frontend --type=LoadBalancer --name=publicfrontend
    # Repeat the process for the *reverseproxy* deployment. 
```
For autoscaling the pods first install the kubernetes metric server
```bash
    kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.5.0/components.yaml
```

Then add autoscaling to deployments
```bash
    kubectl autoscale deployment backend-user --cpu-percent=70 --min=3 --max=5
```