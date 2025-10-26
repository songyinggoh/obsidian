Different types of resources need to communicate with each other via different network protocols to secure the system and minimize costs

# CloudVPN
Securely connects on-premises network to Google Cloud VPC network
Up to 3 GBps
May be configured for static and dynamic routing via cloud router
IKEv1 & IKEv2 ciphers

# VPN Gateway as a service
Used on-premises to connect local to VPC network

![[Screenshot 2025-09-25 124200.png]]
- [ ] Setup VPN gateway in on-premises network and CloudVPN in Google Cloud VPC
	- [ ] Connect them for secure encrypted comms

# Cloud Router
A service that works on CloudVPN or Cloud Interconnect connections to provide dynamic routing by using Border Gateway Protocol

![[Screenshot 2025-09-25 124601.png]]

# Dedicated Interconnect 
For providing direct physical connections between on-premises and Google's networks
Cost effective for transferring large amounts of data

![[Screenshot 2025-09-25 124813.png]]
- Provision a cross connect between Google network and own router at a common colocation facility
- Configure Border Gateway Protocol session over the interconnect between Cloud Router and on-premises router for user traffic from on-premises network to reach VPC network's Google Cloud resources, and vice versa

## To create Dedicated Interconnect connection:
- [ ] Order Dedicated Interconnect
- [ ] Send LOA-CFAs to Google Cloud
- [ ] Test Interconnection
- [ ] Create VLAN attachments and establish Border Gateway Protocol sessions

# Partner Interconnect (with supported service providers)
Useful when:
- If data center is in physical location that is unable to reach a Dedicated Interconnect colocation facility
- Data needs do not warrant a Dedicated Interconnect

![[Screenshot 2025-09-25 130531.png]]
- [ ] Establish connectivity with service provider
- [ ] Request Partner Interconnection from service provider
- [ ] Establish a Border Gateway Protocol session between Cloud Router and on-premises router to start passing traffic between networks

# Connecting two Google Cloud VPCs using VPC peering

![[Screenshot 2025-09-25 131026.png]]
