Moving a database into new Cloud environment with no changes
# Warning:
- Understand license agreements for software running on VMs before moving software from one platform to another
- Ensure SSH keys are transported along with the image after moving for log in purposes
- Not all images created can be exported (e.g. AWS Marketplace)
# Best for:
Monolithic apps (wordpress site)
Legacy apps (ASP.NET app that can't be containerized and impractical to rewrite, and small departmental databases)
Apps that are already virtualized on-premises or in another cloud

- [ ] First, create image of VM in current environment
- [ ] Export image from current environment
	- [ ] Then, copy image to Cloud Storage bucket
- [ ] Create Compute Engine image from exported image
	- [ ] Use Compute Engine image to create VM

# Exporting images from another cloud provider
- [ ] First create image of VM
- [ ] Export image from native cloud provider format to VMware or Microsoft image

![[Screenshot 2025-09-23 001046.png]]
^An AWS EC2 image is exported to VMWare format and saved in S3 bucket

# Migrating many VMs with Migrate to Virtual Machine
Works with VMware, AWS, Azure
<10min compute migration, and data is streamed to Google Cloud before going into production environment
As this process is automated, testing and rollbacks to previous environment is easy

# Steps in VM migration

![[Screenshot 2025-09-23 002307.png]]

# Database migration tooling

## Assess 
migVisor:
- Easy migration from Oracle and SQL Server
- Assessment tool that assists in identifying dependencies and dependences
- Recommends target services and databases

## Migrate
striim:
- Online database migration
- Supports many different database types as both target and source databases