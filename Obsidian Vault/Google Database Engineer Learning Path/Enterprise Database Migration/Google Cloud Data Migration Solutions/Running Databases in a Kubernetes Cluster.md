# What is Kubernetes?
An open-source, cross-platform framework for running applications inside a cluster of shared resources

# To use Kubernetes:
- [ ] First, package app inside a Docker image
	- [ ] Images are provided by vendor or 3rd party and downloaded over the internet
	- [ ] After getting the Docker image, config file in YAML format describes how the image is to be deployed in the cluster
- [ ] After acquiring YAML config code, use CLI commands to deploy, manage, and delete deployments
	- [ ] Use CLI to upload YAML config file to the cluster
- [ ] When config is complete, use kubectl to deploy the resources specified in the YAML config files
```
kubectl apply -f mysql-kube-config.yaml
```

-After app has been deployed, Kubernetes ensures that the deployment environment remains healthy. Kubernetes will fix it if it is otherwise

# Why use Kubernetes?
## Automation (most important)
After init config is complete, admin of deployments can be done with CLI cmds, which allows for quick and easy deployments to different development, testing, and production environments.

## Open-source, cross-platform
Widely-used open source project managed by CLOUD NATIVE FOUNDATION w/ many contributors
Supported by Linux and Windows and can be used in public and private clouds

## Supported by all major cloud providers
Supported by AWS (Elastic Kubernetes Service), Microsoft (Azure Kubernetes Service), Google (Google Kubernetes Engine)
Private clouds like RedHat OpenShift is supported by automation like Pivotal Cloud Foundry

## On-premise hybrid deployments
Kubernetes may be installed on a cluster of machines running in own data center
Configure software once and deploy to any cloud without any significant change to config

# Kubernetes Cluster architecture
1. A cluster (1 or more control planes and some worker nodes) is needed to use Kubernetes
2. Commands and configurations are sent to control plane
3. Control plane stores the configuration and deploys apps to worker nodes
4. Control planes monitors apps to ensure they are running as expected
	1. Should something fail, the control plane fixes it
![[Screenshot 2025-09-16 211252.png]]

# Using GKE (Google Kubernetes Engine) to automate creation of Kubernetes clusters

Nodes are pre-config VMs running in Compute Engine and managed by GKE 
![[Screenshot 2025-09-16 211939.png]]

# Using Google Cloud SDK to automate creation of Kubernetes clusters

Using Google Cloud SDK and CLI,
```
gcloud container clusters create kubernetes-cluster --zone=REPLACE WITH ZONE -- project=REPLACE WITH PROJECT ID
```
Only Zone and Project ID are specified, all else (machine size, no. of nodes, security settings) are default settings.


---
# To send commands to Kubernetes clusters
- [ ] To connect to GKE cluster
```
gcloud container clusters get-credentials kubernetes-cluster --zone=REPLACE WITH ZONE -- project=REPLACE WITH PROJECT ID
```
- [ ] After connecting, use kubectl to send commands to it (already installed in Cloud Shell and included with GC SDK)
	- [ ] Cloud Native Foundation (responsible for maintainence) also provides CLI to be downloaded


# YAML config
To configure a database, such is needed:
1. Persistent Volume Claims
	1. Reserving disk space from the cluster for database
2. Configuring deployment
	1. Specify Docker image, resources, volumes, environment variables, etc. 
3. Configure service
	1. Provides access to database from client applications

![[Screenshot 2025-09-16 214228.png]]

## Configuring secrets(passwords)

1. .env variable is being created for the password for the root user(as shown below)![[Screenshot 2025-09-16 214933.png]]


2. Secret(password) being set to keep sensitive data out of config file:
```
kubectl create secret generic mysql-secrets --from-literal=ROOT_PASSWORD="REPLACE WITH PASSWORD"
```

# Helm (package manager for Kubernetes)
For simplification of database deployment to Kubernetes cluster
Open-source package manager for Kubernetes, similar to apt-get in Linux or Chocolaty in Windows

## Helm charts
Helm may be configured to run on the cluster and then automate deployments using Helm charts
Helm charts provide pre-config deployments for apps and databases

1. init Helm on Kubernetes cluster
2. Use Helm install cmd while specifying chart name and any parameters required:![[Screenshot 2025-09-16 224045.png]]