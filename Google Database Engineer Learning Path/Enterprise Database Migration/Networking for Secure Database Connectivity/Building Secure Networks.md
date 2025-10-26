# Secure network config in Google Cloud

 ![[Screenshot 2025-09-24 132814.png]] 
 - All resources need to be in a project
 - VPCs are global resources and provide virtual networking 
 - Subnets are regional resources and are used to create VMs regionally

# Using multiple networks to isolate machines from each other

![[Screenshot 2025-09-24 133113.png]]
VMs within a VPC can communicate using their internal IP address; however, machines from different VPCs need to communicate through external IP address

# Firewall rules to control VM communications using designated ports*

