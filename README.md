This repository is about deploying a multi-container application into a K8s cluster in Google Cloud. 

The application consists of the following components:


A Flask web application that acts as the frontend.

A PostgreSQL database that stores user data.

A Redis caching service that improves the performance of the Flask application.

The flask app connects and interacts with both postgreSQL to retrieve the stored data and Redis Caching service is used to cache frequently accessed data.
The Dockerfile in the repository needs to be built and then tagged and pushed to dockerhub using the following commands:

docker build -t flaskapp:latest .

docker tag flaskapp:latest Dockerhubusername/flaskapp:latest

docker push Dockerhubusername/flaskapp:latest

Provide the repository of the image in FLask deploy.yaml. 

Create 3 namespaces for 3 components and deploy them into a K8s cluster
Create manifest files for deployment, service, comfigMap, secrets for each component.

Apply all manifests to the cluster: Consider flask app as example. The commands used are as follows

kubectl apply -f flask-namespace.yaml

kubectl apply -f flask-configmap-secret.yaml

kubectl apply -f deploy.yaml

