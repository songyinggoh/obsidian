# Compute Engine
Can run Linux and Windows VMs
Supports up to 96 cores and 100s GB storage and 100s TB storage 
Pre-configured VM images with SQL Server already installed
VM creation may be automated with scripts or templates

## To configure VMs to run databases:
1. Select region and zone closest to users
2. Choose boot disk (a persistent disk image that is configured with OS already installed)
3. Specify disk space required for VM

## SQL server
SQL Server 2014-latest may be chosen
Express, Web, Standard, and Enterprise editions are supported
Microsoft License Mobility for Google Cloud program allows for bring-your-own-license, otherwise, the license fee is already included in the hourly rate
Google's VM security features ensures that images have not been tempered with, and tempered images do not boot

## Automating the installation of VM components
Include start-up script that can be added to the machine config directly or can refer to external script in Cloud Storage
Linux machines use Bash shell scripts
Windows machines use Batch, Command, or Powershell scripts
Google Cloud SDK can script creation of CE VM and other infrastructure resources (e.g. networks, firewall rules, instance templates and instance groups)
Google's web console can configure resources and click cmd line link at bottom of window


# Demystifying the Cloud: An Introduction to IaaS and Google Compute Engine

Welcome to the world of cloud computing! If you've ever wondered how modern applications and websites are built and run, you're in the right place. This guide is designed to introduce you to a foundational concept called **Infrastructure as a Service (IaaS)**, which is one of the core building blocks of the cloud. To make this concept tangible, we'll then explore a real-world example: **Google Compute Engine**, a powerful tool that lets you build and run your own virtual computers in Google's global data centers.

--------------------------------------------------------------------------------

### 1.0 What is Infrastructure as a Service (IaaS)?

#### 1.1 The Core Concept: Renting Digital Infrastructure

Infrastructure as a Service (IaaS) is a cloud computing model that provides you with on-demand access to fundamental computing resources—like servers, storage, and networking—over the internet. Think of it as renting the raw materials of computing instead of owning them.

Traditionally, if a company wanted to launch an application, it had to go through a long and expensive process. This involved buying physical servers, finding a specialized room with proper power and cooling to house them, and hiring IT professionals to manage and maintain everything. This approach is slow, costly, and difficult to scale. IaaS solves this problem by allowing you to instantly provision and use computing infrastructure from a cloud provider, paying only for what you use. In short, IaaS lets you _rent_ the digital infrastructure you need, whenever you need it.

#### 1.2 How It Works: The Shared Responsibility Model

In an IaaS model, there is a clear division of responsibilities between you (the user) and the cloud service provider (CSP). The CSP, like Google, is responsible for managing and maintaining all the underlying physical infrastructure—the data centers, cooling, networking hardware, and the virtualization layer that makes it all work.

As the user, you are responsible for managing everything on top of that infrastructure. This gives you a high degree of control and flexibility. Your responsibilities include:

- The operating system (e.g., Linux or Windows)
- Middleware (the software that connects your OS to your applications)
- Your data
- Your applications

This model allows you to focus on building your applications without worrying about the physical hardware underneath. Now that we understand what IaaS is, let's explore why it has become such a popular and powerful model for businesses and developers.

--------------------------------------------------------------------------------

### 2.0 The Top 5 Benefits of Using IaaS

Using an IaaS model provides several key advantages that make it an attractive option for organizations of all sizes.

1. **Cost Savings:** IaaS eliminates the need for large, upfront capital investments in physical hardware. Instead of buying servers, you shift to a pay-as-you-go operational expense model, paying only for the compute, storage, and networking resources that you actually consume.
2. **High Scalability:** You gain the ability to rapidly scale resources up or down to meet fluctuating demand. This means you can instantly accommodate sudden spikes in traffic and then scale back when resources are no longer needed, avoiding the cost of overbuilding capacity.
3. **Increased Efficiency & Productivity:** By offloading the setup and maintenance of physical infrastructure to the cloud provider, your IT teams are freed from manual tasks. This allows them to redirect their time and energy toward more strategic activities that drive innovation.
4. **Reliability:** IaaS platforms are designed with no single point of failure. The cloud infrastructure offers built-in redundancy and fault tolerance, meaning that if one hardware component fails, your service will remain available.
5. **Faster Time to Market:** IaaS makes it fast and affordable to test new ideas and products. You can spin up the necessary computing infrastructure in minutes—not weeks or months—which significantly speeds up development lifecycles.

These powerful benefits are unique to IaaS, which occupies a specific place in the broader landscape of cloud services.

--------------------------------------------------------------------------------

### 3.0 Placing IaaS in the Cloud Landscape: IaaS vs. PaaS vs. SaaS

IaaS is one of three main cloud service models, each offering a different level of management and abstraction. Understanding the difference between them—Infrastructure as a Service (IaaS), Platform as a Service (PaaS), and Software as a Service (SaaS)—is key to understanding the cloud. The primary difference is how much of the "stack" you manage versus how much the provider manages for you.

|   |   |   |
|---|---|---|
|Service Model|What It Provides|You Manage|
|**IaaS (Infrastructure as a Service)**|The fundamental computing infrastructure (servers, storage, networking). E.g., Compute Engine.|The operating system, middleware, data, and applications.|
|**PaaS (Platform as a Service)**|The infrastructure PLUS the software features and tools for application development. E.g., App Engine.|The code, applications, and data.|
|**SaaS (Software as a Service)**|A complete, ready-to-use application and all the infrastructure needed to deliver it. E.g., Google Workspace.|Nothing but your connection to the app. The provider manages everything.|

Now, let's move from the theoretical to the practical by looking at a leading example of an IaaS product.

--------------------------------------------------------------------------------

### 4.0 IaaS in Action: Introducing Google Compute Engine (GCE)

#### 4.1 What is Google Compute Engine?

Google Compute Engine (GCE) is Google's primary Infrastructure as a Service (IaaS) product. It allows you to create and run self-managed virtual machines (VMs) on Google's global infrastructure. These VMs are essentially your own private computers in the cloud, giving you complete control over the operating system and software you want to run. GCE offers support for a wide range of operating systems, including many versions of both Linux and Windows.

#### 4.2 Key Features of GCE

GCE provides a powerful and flexible set of features that allow you to tailor your virtual machines to your exact needs.

- **Virtual Machines (VMs) and Operating Systems:** GCE can create very large and powerful VMs, with configurations offering up to 96 cores and hundreds of gigabytes of RAM. To get you started quickly, it provides many preconfigured public operating system images for both Linux and Windows, which come with the OS already installed.
- **Diverse Machine Types:** Recognizing that different workloads have different needs, GCE offers a comprehensive set of machine families. These include general-purpose machines for balanced workloads, compute-optimized machines for processor-intensive tasks, and memory-optimized machines for applications that require large amounts of RAM. If the predefined options don't fit, you can also create a custom machine type with the specific amount of CPU and memory you require.
- **Flexible Storage Options:** Every VM needs storage, and GCE offers several high-performance options, each with distinct characteristics.

|   |   |
|---|---|
|Storage Type|Key Characteristic|
|**Local SSD**|Offers the best performance but is not durable (data is lost if the instance is stopped).|
|**Hyperdisk**|The fastest _durable_ storage option, where data is preserved when an instance is stopped.|
|**Persistent Disk**|Durable block storage used for machine types that don't support Hyperdisk.|

These features provide the building blocks, and one of the most powerful aspects of IaaS is the ability to assemble these blocks automatically.

--------------------------------------------------------------------------------

### 5.0 Automating Your Infrastructure with GCE

A key advantage of IaaS is the ability to programmatically create and configure your infrastructure. Instead of manually clicking through a console, you can use scripts to build entire environments in a repeatable and automated way.

#### 5.1 Using Startup Scripts

When you configure a GCE virtual machine, you can include a **startup script**. This script runs automatically the first time the VM boots, allowing you to automate the installation of components and software. For Linux machines, you can use **Bash** scripts, and for Windows machines, you can use **Powershell** or batch scripts.

#### 5.2 Using the Google Cloud SDK (gcloud)

The Google Cloud SDK is a command-line tool that allows you to manage all your Google Cloud resources, including scripting the creation of Compute Engine VMs. For example, the single command below creates a complete Linux VM, configures its size, and uses a startup script to install a MySQL database server.

```bash
gcloud compute instances create db-server --project=project-id-here --zone=us-central1-a --machine-type=n1-standard-4 --metadata=startup-script=\#\!\ /bin/bash$'\n'apt-get\ update$'\n'apt-get\ install\ -y\ mysql-server$'\n'systemctl\ status\ mysql --boot-disk-size=10GB
```

Here is a breakdown of what this command does:

- `gcloud compute instances create db-server`: Creates a new VM instance named `db-server`.
- `--zone=us-central1-a`: Places the VM in a specific data center location.
- `--machine-type=n1-standard-4`: Specifies a standard machine type with 4 virtual CPUs.
- `--metadata=startup-script=...`: Passes in a startup script that updates the system and installs a MySQL server.
- `--boot-disk-size=10GB`: Sets the size of the operating system disk to 10 gigabytes.

--------------------------------------------------------------------------------

### 6.0 Conclusion: Your First Step into IaaS

Infrastructure as a Service provides the fundamental building blocks of modern cloud computing, offering incredible flexibility, reliability, and scalability. By allowing you to rent and automate computing resources on demand, IaaS empowers developers and businesses to build and innovate faster than ever before, and Google Compute Engine stands as a powerful, real-world example of this transformative model in action.