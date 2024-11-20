This repository is about deploying a multi-container application into a K8s cluster in Google Cloud. 

The application consists of the following components:


A Flask web application that acts as the frontend.

A PostgreSQL database that stores user data.

A Redis caching service that improves the performance of the Flask application.

The flask app connects and interacts with both postgreSQL to retrieve the stored data and Redis Caching service is used to cache frequently accessed data.
The Dockerfile in the repository needs to be built and then tagged and pushed to dockerhub using the following commands:
```
docker build -t flaskapp:latest .

docker tag flaskapp:latest Dockerhubusername/flaskapp:latest

docker push Dockerhubusername/flaskapp:latest
```
Provide the repository of the image in FLask deploy.yaml. 

Create 3 namespaces for 3 components and deploy them into a K8s cluster
Create manifest files for deployment, service, comfigMap, secrets for each component.

Note: The postgreSQL database and Redis Cache should be deployed first, as the flask-app is dependent on both the services.

Apply all manifests to the cluster: Consider flask app as example. The commands used are as follows
```
kubectl apply -f flask-namespace.yaml

kubectl apply -f flask-configmap-secret.yaml

kubectl apply -f deploy.yaml
```
Initialize the database: Get the pod name using the command : 
```
kubectl get pods -n postgres-db
```
Access the pod: 
```
kubectl exec -it <postgres-pod-name> -n postgres-db -- bash
```
```
Create table and insert sample data: psql -h localhost -U <user> -d <dbname>
CREATE TABLE users (ID INT PRIMARY KEY NOT NULL, NAME TEXT NOT NULL);
INSERT INTO users VALUES (1, '3324_1');
```
Access the application using external IP

http://ExternalIP/ will provide us with the flask-app home page

http://ExternalIP/users will retrieve the users

http://ExternalIP/cache will provide the Redis Cache service



I had used SOPS to encrypt the secrets by providing key ring from google cloud.

Install SOPS and verify the installation by checking the version: sops -v

- Create SOPS configuration file .sops.yaml
```
# .sops.yaml
creation_rules:
  - gcp_kms: projects/<project>/locations/<location>/keyRings/<key-ring-name>/cryptoKeys/<key-name>
    # Specify any file patterns you want to auto-encrypt
    path_regex: '.*\.yaml$'
    encrypted_regex: '^(data)$'
```
Provide the gcp-kms from any cloud/service you use.

Encrypt, Edit, Decrypt, and Apply Secrets with sops
You can use sops to securely manage your Kubernetes secrets. Follow these steps:

1. Encrypt a Secrets File
To encrypt a plain secrets.yaml file:
```
sops --encrypt secrets.yaml > secrets.enc.yaml
```
2. Edit the Encrypted Secrets
To safely edit the encrypted secrets.enc.yaml file:
```
sops secrets.enc.yaml
```
3. Decrypt the Encrypted Secrets
To decrypt the file back into plaintext (for inspection or debugging):
```
sops -d secrets.enc.yaml > secrets.yaml
```
4. Create a Secret Resource in Kubernetes
To apply the encrypted secrets directly to your Kubernetes cluster:
```
sops -d secrets.enc.yaml | kubectl apply -f -
```
