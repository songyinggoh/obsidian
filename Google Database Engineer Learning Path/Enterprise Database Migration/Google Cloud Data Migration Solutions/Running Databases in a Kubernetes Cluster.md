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


# Implementation Guide: Deploying a Production-Ready MySQL Database on Google Kubernetes Engine (GKE)

## 1.0 Introduction: The Strategic Case for Containerized Databases

As containerization solidifies its role as the standard for modern application deployment, DevOps professionals and solutions architects are increasingly tasked with deploying stateful services, such as databases, within Kubernetes environments. While managing state presents unique challenges in a distributed system, the benefits of consistency, automation, and portability are compelling. This guide serves as a practical, step-by-step walkthrough for deploying a MySQL database on Google Kubernetes Engine (GKE), addressing the core architectural decisions and configurations required for a successful implementation.

However, not all databases are equally suited for containerization out-of-the-box. It is easier to run a database on Kubernetes if it includes concepts like sharding, failover, and replication built into its DNA—examples include ElasticSearch, Cassandra, or MongoDB. Traditional relational databases like MySQL and PostgreSQL, which were not designed for this cloud-native model, require more careful architectural consideration. While this guide focuses on MySQL, be aware that these traditional databases often benefit from advanced tools called "Operators" to manage production concerns, a topic we will revisit in the conclusion.

Running any database on Kubernetes provides several strategic advantages that align with modern DevOps principles:

- **Automation and Reproducibility**: Kubernetes enables the automation of the entire compute infrastructure. Once the initial configuration is defined, deployments become reproducible and testable, allowing for simple, script-based commands to create, update, and remove database instances. This facilitates rapid and reliable promotion across development, testing, and production environments.
- **Cross-Platform and Hybrid Cloud Capabilities**: As an open-source, cross-platform framework, Kubernetes is supported by all major cloud providers, including Google Cloud (GKE), AWS (EKS), and Azure (AKS). Furthermore, it can be deployed on-premises, enabling true hybrid deployment scenarios. This allows organizations to define a database configuration once and deploy it to any cloud or private data center with minimal changes.

While powerful, this approach requires a clear understanding of the operational trade-offs. The decision to run a database on Kubernetes fits within a spectrum of management options.

|   |   |   |
|---|---|---|
|Option|Management Overhead|Key Considerations|
|**Fully managed databases (e.g., Cloud SQL)**|Low|Google Cloud handles backups, patching, and scaling. This is a low-ops choice, but you may not have access to specific versions or extensions.|
|**Run it on Kubernetes (e.g., GKE)**|Medium-High|Provides powerful automation for keeping the database application running, but you are responsible for database-specific tasks like backups and scaling. This is closer to a "full-ops" model.|
|**Do-it-yourself on a VM**|High|The "full-ops" option. You have complete control and access to all features but are fully responsible for reliability, scaling, backups, and all maintenance.|

This guide will focus on the "Run it on Kubernetes" model, providing the foundational knowledge to leverage its automation benefits. We will begin by exploring the core components of the Kubernetes architecture.

## 2.0 Foundational Concepts: Understanding the GKE and Kubernetes Environment

Before deploying any application, it is crucial to understand the underlying architecture of the platform. A clear grasp of the core components of a Kubernetes cluster and the value provided by a managed service like Google Kubernetes Engine (GKE) is essential for effective deployment and troubleshooting. This section will demystify these foundational concepts.

A **Kubernetes cluster** is a set of machines, called nodes, that are grouped together to run containerized applications. A cluster consists of two primary components:

1. **The Control Plane**: Acting as the brain of the cluster, the control plane is responsible for managing the cluster's overall state. It stores the configuration, schedules applications onto worker nodes, and monitors the cluster to ensure the deployed environment remains healthy and matches the desired state. All administrative commands, typically sent via the `kubectl` command-line interface (CLI), are processed by the control plane.
2. **Worker Nodes**: These are the machines (virtual or physical) that perform the actual work of running application containers. Each worker node contains the necessary services to manage container execution and communicates with the control plane to receive instructions and report its status.

**Google Kubernetes Engine (GKE)** is a managed service that dramatically simplifies the creation and operation of Kubernetes clusters on Google Cloud. GKE provides the entire control plane as a highly available, scalable, and managed service. This is a significant operational advantage, as it abstracts away the complexity of managing critical control plane components like the `etcd` key-value store (which holds the cluster state), the API server, and the scheduler. In a self-managed cluster, ensuring these components are available and scalable is a major engineering burden. With GKE, users can offload that responsibility and focus on configuring the worker nodes, which are essentially preconfigured Compute Engine virtual machines.

With this architectural context in mind, we can now proceed to the practical steps of provisioning a GKE cluster.

## 3.0 Step 1: Provisioning and Accessing Your GKE Cluster

The first hands-on step in our deployment is to create a robust and reliable Kubernetes cluster. Using Google Kubernetes Engine and the Google Cloud SDK, we can automate and simplify this critical process with a few simple commands.

First, we will use the `gcloud` CLI to create the cluster. This command instructs GKE to provision the necessary resources, including the managed control plane and the specified worker nodes.

```bash
gcloud container clusters create kubernetes-cluster --zone=us-central1-a --project=project-id-here
```

Let's break down the components of this command:

- `gcloud container clusters create kubernetes-cluster`: This is the core command, which initiates the creation of a GKE cluster named `kubernetes-cluster`.
- `--zone=us-central1-a`: This parameter specifies the geographical location where the cluster's nodes will be provisioned.
- `--project=project-id-here`: This specifies the Google Cloud project ID under which the cluster resources will be created.

Note that GKE provides sensible defaults for many parameters. In this example, we have not specified the machine size or the number of nodes, so GKE will use its default settings for these values.

Once the cluster has been created, you must configure your local environment to communicate with it. The following command retrieves the necessary credentials and configures `kubectl` to target the new cluster.

```bash
gcloud container clusters get-credentials kubernetes-cluster --zone=us-central1-a --project=project-id-here
```

This `get-credentials` command ensures that subsequent `kubectl` commands are sent to the correct cluster's control plane. To verify the connection and see the provisioned worker nodes, you can run `kubectl get nodes`.

```bash
$ kubectl get nodes
NAME                                                 STATUS   ROLES    AGE   VERSION
gke-kubernetes-cluster-default-pool-5fc4b15b-b3x8   Ready    <none>   48m   v1.14.10-gke.27
gke-kubernetes-cluster-default-pool-5fc4b15b-s1f0   Ready    <none>   48m   v1.14.10-gke.27
gke-kubernetes-cluster-default-pool-5fc4b15b-z7ss   Ready    <none>   48m   v1.14.10-gke.27
```

The output confirms that three worker nodes are running and in a `Ready` state, and that `kubectl` is successfully communicating with the GKE control plane. With the cluster infrastructure in place, the next step is to define the MySQL database resources using YAML configuration files.

## 4.0 Step 2: Configuring the MySQL Deployment Resources via YAML

In Kubernetes, the desired state of an application is described declaratively using YAML configuration files. This infrastructure-as-code approach is central to Kubernetes' power and reproducibility. To run a stateful database like MySQL, we must define three essential Kubernetes objects: a `PersistentVolumeClaim` for storage, a `Deployment` for the application container, and a `Service` for network access.

- `**PersistentVolumeClaim**`: Reserves persistent disk space from the cluster for your database.
- `**Deployment**`: Configures the application itself, specifying the Docker image, required resources, volume mounts, and environment variables.
- `**Service**`: Provides a stable network endpoint for client applications to connect to the database.

### 4.1 Defining Persistent Storage with `PersistentVolumeClaim`

The most critical consideration for running a database on Kubernetes is managing state. Because pods (the containers running the application) are transient and can be restarted or moved at any time, their local filesystems are ephemeral. To ensure data durability, we must attach persistent storage. The `PersistentVolumeClaim` (PVC) is the Kubernetes object used to request and reserve a block of storage from the cluster.

The following YAML configuration defines a PVC for our MySQL database.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-data-disk
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

- `kind: PersistentVolumeClaim`: Specifies the type of Kubernetes object being created.
- `metadata.name: mysql-data-disk`: Assigns a unique name to the PVC, which we will reference later in our Deployment.
- `accessModes: - ReadWriteOnce`: Defines how the volume can be mounted. `ReadWriteOnce` means the volume can be mounted as read-write by a single node at a time. This is the correct choice for a single-instance MySQL database, as its underlying file system is not designed for concurrent writes from multiple pods.
- `resources.requests.storage: 1Gi`: Requests 1 Gibibyte of storage space from the underlying cloud provider.

_**Architect's Note:**_ _For a production environment, this value must be sized based on careful capacity planning. You can also specify a_ `_storageClassName_` _in the_ `_spec_` _to request different performance tiers, such as_ `_pd-ssd_` _for faster I/O, which is critical for database performance._

### 4.2 Managing Secrets for Database Credentials

It is a critical security best practice to keep sensitive data, such as database passwords, out of configuration files and source control. Kubernetes provides a dedicated object called a **Secret** for this purpose. We will create a Secret to securely store the MySQL root password.

Use the following `kubectl` command to create the secret imperatively:

```bash
kubectl create secret generic mysql-secrets \
  --from-literal=ROOT_PASSWORD="Super-secret-pa$$word-Here!"
```

This command creates a Secret object named `mysql-secrets` containing a single key-value pair: the key is `ROOT_PASSWORD` and the value is the specified password string. Our Deployment configuration will reference this Secret by name rather than exposing the password directly in the YAML file.

_**Architect's Note:**_ _While_ `_kubectl create secret_` _is convenient for development, production workloads should integrate with a dedicated secrets management system like Google Secret Manager or HashiCorp Vault. These systems provide robust auditing, access control, and secret rotation capabilities._

### 4.3 Creating the Core `Deployment` Configuration

The `Deployment` is the core Kubernetes object that instructs the cluster how to run our application. It defines everything from the container image to pull, the ports to expose, and how to connect the persistent storage and secrets we defined previously.

Below is the annotated YAML configuration for our MySQL `Deployment`.

```yaml
apiVersion: apps/vv1
kind: Deployment
metadata:
  name: mysql-deployment
  labels:
    app: mysql
spec:
  replicas: 1
  # The 'selector' is required to link this Deployment to its pods.
  # It has been omitted for brevity.
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:5.7
          ports:
            - containerPort: 3306
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secrets
                  key: ROOT_PASSWORD
          volumeMounts:
            - mountPath: "/var/lib/mysql"
              subPath: "mysql"
              name: mysql-data
      volumes:
        - name: mysql-data
          persistentVolumeClaim:
            claimName: mysql-data-disk
```

Let's deconstruct the key sections of this configuration:

1. **Image and Port**: The `spec.template.spec.containers` section defines the container to run.
    - `image: mysql:5.7`: Instructs Kubernetes to pull the official MySQL version 5.7 image from a container registry (like Docker Hub).
    - `containerPort: 3306`: Opens the default MySQL port `3306` inside the container, making it available for network connections.
2. **Attaching Storage**: The `volumes` and `volumeMounts` sections work together to connect our persistent disk to the container.
    - The `volumes` section declares a volume named `mysql-data` and specifies that its source is the `persistentVolumeClaim` with the `claimName: mysql-data-disk`. This connects the Deployment to the PVC we created earlier.
    - The `volumeMounts` section then attaches the `mysql-data` volume to a specific path inside the container. The `mountPath: "/var/lib/mysql"` is the default directory where MySQL stores its data files.
3. **Injecting the Secret**: The `env` section configures environment variables inside the container.
    - This configuration creates an environment variable named `MYSQL_ROOT_PASSWORD`.
    - Instead of a hardcoded value, `valueFrom.secretKeyRef` tells Kubernetes to populate this variable's value by looking up the `key: ROOT_PASSWORD` from the Secret named `mysql-secrets`.

### 4.4 Exposing the Database with a `Service`

While the Deployment runs our database pod, pods are ephemeral and their IP addresses can change. A Kubernetes `Service` provides a stable network endpoint—a persistent IP address and DNS name—that acts as a reliable load balancer for one or more pods. Client applications connect to the Service, which then routes traffic to the healthy database pod.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  selector:
    app: mysql
  ports:
  - protocol: TCP
    port: 3306
    targetPort: 3306
```

- `spec.selector.app: mysql`: This is the crucial link. The Service uses this label selector to discover all pods with the matching label `app: mysql` (which we defined in our Deployment's template metadata). This powerful decoupling means the Service provides a stable endpoint regardless of the underlying pods. You can scale, update, or replace the pods, and as long as the new ones have the correct label, the Service will automatically find and route traffic to them.
- `ports`: This section defines the port mapping. It maps incoming TCP requests on the Service's `port: 3306` to the `targetPort: 3306` on the container.

With our storage, secrets, deployment, and service configurations defined, we are ready to apply them to the cluster.

## 5.0 Step 3: Applying the Configuration and Deploying MySQL

With all the necessary YAML configurations defined and saved into a single file (e.g., `mysql-kube-config.yaml`), the final step is to submit this desired state to the Kubernetes cluster. The `kubectl apply` command reads the configuration file and instructs the control plane to create or update the specified resources.

```bash
kubectl apply -f mysql-kube-config.yaml
```

This single command instructs Kubernetes to create the `PersistentVolumeClaim`, the `Deployment`, and the `Service` as defined in the file. The Kubernetes control plane will now work to achieve this state, which involves provisioning a persistent disk, pulling the `mysql:5.7` container image, starting the container, and attaching the storage and secrets.

To remove all the resources created by this configuration, you can use the corresponding `delete` command: `kubectl delete -f mysql-kube-config.yaml`.

While this manual process of writing YAML files is powerful and fundamental to understanding Kubernetes, tools are available to simplify the deployment of common applications.

## 6.0 Alternative Deployment: Simplifying with Helm

For those who find the manual creation of multiple YAML files overwhelming, there is an easier and arguably better way to deploy a database into a cluster. **Helm** is an open-source package manager for Kubernetes, analogous to `apt` or `yum` on Linux. It simplifies the deployment and management of applications.

Helm uses packages called **Charts**, which are preconfigured collections of all the necessary Kubernetes YAML files for a given application. A large community creates and maintains Charts for hundreds of applications, including databases like MySQL, SQL Server, and PostgreSQL. These can be found on public repositories like the Helm Hub website.

The process for using Helm involves two main steps: initializing Helm on the cluster (`helm init` for older versions, or just adding a repo for Helm 3+) and then installing an application using its Chart. For example, deploying a production-ready MySQL instance can be done with a single command:

```bash
helm install my-mysql bitnami/mysql \
  --set auth.rootPassword=my-strong-password
```

This single line demonstrates the significant simplification Helm offers for standard deployments. The `bitnami/mysql` chart packages all the necessary configurations for a `Deployment`, `Service`, `PVC`, and `Secret` into a reusable and customizable format, eliminating the need to write custom YAML.

Choosing between manual YAML and Helm involves a strategic trade-off. Manual YAML provides granular control and deepens your understanding of Kubernetes objects, which is invaluable for troubleshooting complex issues. Helm, on the other hand, prioritizes speed, standardization, and leverages community-vetted best practices, but at the cost of abstraction. For standard applications, Helm is often the superior choice for its efficiency, while custom or highly-tuned workloads may benefit from the explicit control of manual configuration.

## 7.0 Conclusion and Advanced Considerations

In this guide, you have successfully walked through the process of creating a GKE cluster and deploying a MySQL database using both a manual YAML configuration and the simplified Helm approach. You have configured persistent storage, managed secrets securely, and exposed the database with a stable network service, establishing a solid foundation for running stateful applications on Kubernetes.

For production environments, especially for databases like MySQL and PostgreSQL that do not have native Kubernetes orchestration features, it is important to consider more advanced management techniques. For these scenarios, specialized tools called **Operators** can manage complex database-specific tasks such as backups, scaling, and replication directly through the Kubernetes API.

Projects like the **Oracle MySQL Operator** and **Crunchy Data for PostgreSQL** are designed to simplify day-2 operations. These Operators extend Kubernetes with custom resources, allowing you to perform database-specific actions with simple `kubectl` or CLI commands. For example, using the Crunchy Data operator, a backup can be initiated with a command like `pgo backup [cluster_name]`, abstracting away the complex underlying procedures.

While running a database in Kubernetes is gaining traction, it is still far from an exact science. As a final thought, "keep an eye out as technologies and tools evolve toward making running databases in Kubernetes much more the norm."