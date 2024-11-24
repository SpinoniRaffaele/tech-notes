Azure provides more than 100 services that enable you to do everything from running your existing applications on virtual machines to exploring new software paradigms, a cloud is not just a set of VMs but other tools ready to use and integrated in the environment.

In order to use Azure services you need an Azure **subscription**, with an **Azure account** one subscription is automatically created, but multiple ones can be added. Inside each subscription there can be multiple **resource groups** where different azure services are grouped.

Each subscription has its own invoice, and it's total amount to bill for the resources used

Azure offers a free account with access to 20 Azure services for 1 year, a credit to be used in the year and access to some developer tools.

Azure CLI can be accessed using `az` , for example you can start an interactive shell that helps you to insert azure commands like so:
`az interactive`, then you can type `exit`.

### Physical Infrastructure
Azure is made of datacenters, which are facilities of resources spread around the world. As a customer you don't access datacenter directly but regions and availability zones:
- Region: graphical area that contains at least one or more datacenters that work in a low-latency network
- AZ: they are physically separate datacenters in an Azure region. Availability Zones are isolation boundaries in the sense that if a zone goes down the other continues working. Different AZ in the same region are connected through a fiber-optic network. 
  There are at least 3 AZ in each region supporting AZ.
Using resources in multiple AZs will make a resilient and more available system. AZ are primarily for VM, managed disks, load balancer and SQL databases.
In Particular Azure distinguishes:
- **zonal services**: services that are attached to a zone (VMs)
- **zone-redundant services:** automatically replicated across the AZs (SQL DB)
- **non-regional services**: services always available globally, resilient to zone-wide outages.

**Region Pairs**: most Azure regions are paired with another region at lease 300 miles away. This allow for replication of resources in a region pair, in order to be resilient to massive issues that impact an entire region. (ex: West US + East US). Some services are automatically replicated.

**Sovereign Regions**: Instances of azure that are isolated from the main instance of Azure (used by governments, ex: US Gov Virginia, China East)

### Management Infrastructure
A **resource** is the basic building block for azure, anything you create, provision, deploy, etc... is a resource.
**Resource group**: a group of resources, each resource must be in a unique resource group, resource groups cannot be nested and the resources can be moved from group A to group B.
When you perform an action on a resource group you are doing the same action for all the resources inside.

**Subscription**: unit of management, billing and scale. They allow to logically group resource groups to facilitate billing and management. You can implement boundaries between subscriptions:
- Billing boundaries (different way of billing resources)
- Access Control boundaries
You can use subscriptions to separate environment (testing and prod), or different structures inside the organization, or different billing purposes (so you can easily track costs in grouped sections).

**Management group**: management level above subscriptions, used to apply common governance conditions to the different subs. Used for enterprise-grade management. Management group can also be nested. Management groups also apply role-based access control that will be inherited by all the entities underneath. 10000 management groups are the limit for a single directory, the nesting can reach up to 6 levels.

# Computing Components
## Azure virtual Machines
They are a type of IaaS, it is a virtualized server that can be user in any way with any software, you have total control over the OS, the hosting configuration and the software inside. You can create VM from an array of predefined image to have an OS ready in minutes. When choosing a VM you need to specify the resources associated: size (cores and RAM), storage disks, networking (public IP addresses, v net, port configuration)

**VM Scale Sets**: this azure component allow you to manage, configure, and update azure config a set of VMs together in one shot, you can set up scalability to increase/decrease the amount of machines in the set. Under the hood the scale sets deploys a load balancer that makes sure to balance the load across the VMs. (imagine you are patching the OS of one of the machines, the load balancer will redirect traffic to the other instances of the set)

**VM Availability sets**: an azure component that uses varied power and network connectivity to various VMs to have a logical reliable unit. It accomplishes this in two ways:
- Update domain: is a grouping of VMs that can be updated at the same time. An availability set spans across multiple update domain allow to apply rolling update so that only one VM at a time is down. (you can ensure the same updates with VM availability sets)
- Fault Domain: a group of VM with common power source and network switch. An availability set spans across 3 fault domains.
Availability sets are free and you pay only for the VM instances that you create.

VMs are also an excellent choice when you move from a physical server to the cloud (also known as lift and shift). You can create an image of the physical server and host it within a VM with little or no changes. Just like a physical on-premises server, you must maintain the VM: you’re responsible for maintaining the installed OS and software.

**Azure Virtual Desktop**: types of VM, it enables the usage of a cloud-hosted OS from a virtual remote control (it works with many apps that offers remote desktop solutions, or even in the browser) With Azure virtual desktop, users connect to the remote VM in a secure v net, the manager of the resource can decide how many user can connect to it at the same time.
You can scale up and down as needed. the app will work and look like it's local (except for a small icon identifying that is it is virtual). This enhances security because it's centralized and the users don't have to worry. The data and the apps are separated from the client's hardware making the risk of confidential data leak smaller.

**Azure containers Instances**: containers are virtualization environments, you can run multiple different OS containers inside the same virtual host. Containers are more light-weight w.r.t VMs and they are designed to be created, scaled and stopped frequently. Azure supports Docker as a container engine.
VM virtualize the hardware while Containers virtualize the operating system. With containers you can run multiple OS in the same host. Containers are more portable and more portable.
Containers are a PaaS offering because they manage the underlying VM.
**Azure container Apps** are a more advanced solution that allows you to run an app right away, this service removes the container management responsibility from the customer to the cloud provider.
**Azure Kubernetes service**: a more complete solution offering also the container management of Kubernetes

## Azure Functions
It's a service for event-driven, serverless backend computation that doesn't require VM or Containers. Azure functions don't consume resources because they are not running, but they are woken up when the specified event occurs. Its called serverless but there are actual server running the function, but they are managed for you by the provider. Serverless automatically scale and apply high availability, they are also cheap because you only pay for the time in which your code runs. They are useful when you are only concerned about the code running and not the infrastructure. Functions can be stateful (Durable Functions), they use a context passed as input to track the state.

### Azure App Service
An alternative to VMs and Containers, App Service are a robust hosting option, it's based on HTTP and it supports multiple languages (.NET, Java, ruby, node.js, Python), it's ideal to host a REST API.
It can be used for
- web apps
- API
- WebJobs (any program or script that can be run by a trigger, useful background asynchronous tasks linked with your app)
- Mobile apps
With App Services, deployment (directly from code repository in github) and management are integrated into the platform, endpoints can be secured, scalability is automatically provided with built-in load balancing. You don't have to worry about the OS or the container, it is configured at startup and handled by Azure

# Network Components
**Virtual Networks** and **Virtual subnets** are used to make azure resources communicate with each other, with the internet and with other on-premises services, they provide isolation and segmentation of networks, they perform routing and filtering of the traffic.
- **Public endpoints**: public IP accessible in the internet
- **Private endpoints**: internal to a virtual network they have a private IP address from the address space of that vnet.

When you create a vnet you set up a private IP address space that exists only inside the network, you can further split the IP range in subnets. For name resolution you can use the Azure service built-in or using external DNS.

To make a resource accessible from the whole internet you need to create a **public IP** and assign it, otherwise you can put the resource behind a public load balancer.

If you want to link different private azure resources you can use **Service Endpoints** (which are not exposed externally).

If you need to link the on-premises resources to the Azure cloud resources, you can use an Azure **VPN**, or **Azure ExpressRoute** which provides a dedicated VPN that doesn't travel over the internet but in private channels (used for high bandwidth and higher levels of security).

Azure automatically handles the routing between the subnets, but yo can override the behavior with custom elements:
- **Route tables:** you can define you own rules of routing
- **Border Gateway Protocol**: used with VPNs to propagate the on-premises BGP routes to Azure virtual networks.

In order to specify custom filtering you can use:
- **Network security groups**: Azure resources that specify multiple inbound and outbound security rules (they work on all the layers, IP, ports, protocol used)
- **Network virtual alliances**: specialized VMs that perform network function (such as running a firewall).

You can link different virtual networks using **virtual network peering**: it uses private channels to send data inside azure backbone network, never entering the public internet.

### VPN
it's an encrypted tunnel inside another network, used to connect more private networks over a public network.
**VPN GATEWAY**: an azure component deployed in a subnet of a virtual network that enables to connect other networks/devices to the virtual network safely through the internet.
There are two types of VPN gateway, you must configure one:
- Policy-based gateway: the packets are send based on a static map of IP address to locations
- Route-based gateway: the tunnels are dynamic to topology changes and the packet is sent on dynamic destination
By default the VPN gateway is deployed in a couple of active/standby gateways underneath.
You can configure the active/active scenario, but in this case you will have 2 IPs exposed to the internet with which you can connect. You can also setup the VPN gateway to use the public internet in case the private Azure infrastructure (ExpressRoute) is not available.

### Azure express Route
It extends your on-premises network into the Microsoft cloud over a (redundant) private connection, the connection is called ExpressRoute circuit. Those connections are more secure because they don't go over the public internet. ExpressRoute Global Reach can be enabled to let datacenter communicate over all-world regions without transferring data over the public internet. Express routes uses Border Gateway Protocol to dynamically route traffic.
Connectivity models:
- CloudExchange colocation: when your facility is physically colocated at a cloud exchange.
- Point-to-Point ethernet connection.
- Any-to-Any connection: WAN connection between on premises and azure
- Directly connect from ExpressRoute sites located all over the world

### Azure DNS
A DNS scaled at Azure level, it is reliable and highly available, it is secure because it provides role-based access control, activity logs to monitor activity, resource locking to lock a subscription, resource group, or resource. With Azure DNS you can use Alias Record to refer to an Azure resource, it will be updated automatically if the unerlying IP address changes.


# Storage Services
Different storage solutions based on the needs. Massive amount of massive binary data (BLOB storage). File storage, ideal for file sharing on the cloud. Disk storage, provide disks for VM and applications, SSD and conventional hard drives, Table stores, great for key value pairs storing. Message queue for storing messages not yet processed. You can optimize the cost by choosing between:
- hot for frequent access
- cool (stored for at least 30 days, for example invoices)
- cold: data stored for at least 90 days and accessed infrequently
- archive (not frequent access, 180 days of storage min, such as backups).
Hot, cool and cold can be set at account level. Archive can be set for individual resources. Cool and cold tiers have less availability but same durability. Archive has the lower cost in terms of storage but the highest in terms of access.

In order to use storage solutions you need to create a storage account, the type of the account will determine the storage service and redundancy options (more on this later).
Account types:
![[Pasted image 20241011153304.png]]
The storage account have an unique name that creates a namespace in azure. You will see the name + service which creates a unique identifier for the service (also an URL).

Storage is durable and highly available thanks to redundancy, it is secure because data is encrypted, it is accessible with a variety of libraries and with an API.

### Redundancy
Azure stores multiple copies of data automatically, choosing the raight redundancy option is a trade off between cost and availability. For your data you have a primary region where it is stored and secondary regions for backup.

Data in azure storage is always replicated 3 times in the primary region, it can be replicated in two ways:
- Locally redundant storage: the 3 copies are in the same datacenter (11 nines of durability), 
- Zone redundant storage: the 3 copies are in 3 AZs (12 nines of durability), it itill restrict data within a country or region to meet governance requirements.

When you select the primary region, the secondary one is based on azure region pairs and it can't be changed. By default the data on the secondary region is not accessible even for read (you can set it to be always accessible for read), it can be activated in case of fail of the primary region (in this case the secondary region becomes the primary one). Notice that the data in the secondary region may not be up-to-date with the primary and there is no SLA on Azure to ensure low sync time. Types of redundancy with secondary region:
- Geo-redundant storage: it is a locally redundant storage in two regions. (16 nines of durability)
- Geo-zone-redundant storage: it is zone redundant in the primary region and locally redundant in the secondary. (16 nines still).
### Azure Blobs
Used to store massive amounts of data, it is unstructured, it can hold any data. It is not limited by file formats, it doesn't require developers to manage disks (contrary to the disk storage). Ideal for image, videos, streaming, storing backups, analytics warehouse.
You can access objects in the blob storage through an API or use client application/libraries.
### Azure files
Fully managed file share solution accessible through network file system protocols or Server message block.
### Azure queues
Service for storing large number of messages. Once stored they can be access anywhere, each individual message can take up to 64Kb. They can be combined with azure functions to take an action when a message is received, in order to perform async tasks.
### Azure disks
block-level storage volumes managed by azure, they are like physical disks, but virualized, ideal to be paired with VMs that require storage.
### Azure tables
Key value pair storage of large amount of data. It is a NoSQL datastore ideal for storing non structure data.

## Data Migration
Azure migrate is a service that help your migration to the cloud, it provides:
- Unified migration platform: a portal to track your migration process.
- Tools: used for assessment and migration:
	- azure migrate: discovery and assessment
	- azure migrate: server migration
	- data migration assistant (for SQL servers)
	- azure database migration service
	- azure app service migration assistant
	- azure data box: physical migration service that ships your proprietary data storage device with data box (80 Tb). The shipping process is tracked by azure. It is suited for scenario where big amount of data needs to be transferred (> 40Tb). It can be used also to export data from azure datacenters (disaster recovery/ security requirements).

For smaller amount of data Azure offers the following:
- AzCopy: command line utility that you can use to copy data to or from your storage account.
- Azure storage explorer: standalone application that has a UI to manage files and blobs in your storage account (it is a UI on top of AzCopy)
- Azure file sync: tool to sync between a local Window server and azure storage, it is bi-directionally synced


# Identity, Access and Security

### Microsoft Entra ID
cloud based identity and access management service.
is a directory service enabling you to access cloud application (Microsoft or not).
Active directory runs on windows server and it is the on-premises equivalent, the cloud version is Entra ID. It lets you control the identity accounts. It also detects suspicious sign-in attempts.
Active directory and Entra ID needs to maintain two sets of identities, however you can synchronize identities between the two enabling a consistent identity experience.

It is used by IT admins to control access to the resources, it can be used by devs to add functionalities to the app they build (like SSO or access to resources).
It can be used by users themselves to manage their account (like self-service password reset).

Services (payed and unlocked by a license) provided by Microsoft Entra ID are:
- authentication: verifying identity, MFA, self-reset, blacklist of passwords,..
- SSO: use one identity across multiple systems and platforms
- Application management: you can use the Entra ID features directly inside the applications that you build
- device management: you can register devices, implement device-based conditional access policies,..

### Microsoft Entra Domain Services
service that provides managed domain services such as domain join, group policy, LDAP. It's a set of fully managed domain services.
It integrates with the existing Microsoft Entra tenant (on-premise domain service).
When you create a Microsoft Entra Domain Services managed domain, multiple domain controllers are deployed into the selected region as a replica set, those are fully managed by azure.
The managed domain perform one-way synchronization from Entra ID to Microsoft Entra Domain Services, so if you create resources directly into the domain services they won't be reflected in Entra ID.

Authentication is the process of establishing the identity of a person, service, or device.
Azure provides passwords, 2FA (provided by Microsoft Entra Multifactor Authentication), SSO and passwordless (once you registered the device, you can sign in providing something you know (a PIN) or something you are (fingerprint)).
Windows Hello for business / Microsoft Authenticator App / FIDO2 security keys (external security key or a key built into a device) are the passwordless solutions that integrate with Microsoft Entra ID.

Microsoft Entra External ID manages all the ways you can securely interact with users outside your org. External users uses their own digital identity (like google or Facebook or another company), the external identity provider manages their identity, Microsoft Entra external ID manages the access to your apps. If the external person comes from another Microsoft Entra org in a B2B context, you can establish a mutual, two-way trust between the orgs for seamless collaboration.
In case you want to give access to customer instead, you can leverage Azure AD B2C for identity and access management.

### Azure Conditional Access
is a tool that Entra ID uses to allow/deny access to resources based on identity signals (who is the user, where is the user, what is the device that the user is using).
Useful to protect the organizational assets.
Conditional Access offers granular configuration of the MFA (user doesn't need the second factor if he is in a known location).
![[Pasted image 20241014181950.png]]
users signal are used to take decisions which might require enforcements (such as 2FA).
You can also completely block access from specific signal condition (access only from managed device, block untrusted sources like unexpected locations).

### Azure role-based access control
service that enables you to control access by using built-in and custom roles that describes common access rules to cloud resources. Instead of managing each user's permission, you create roles (grouping access permissions) and then you can assign roles to individuals. Role based access control is not generally assigned to the single resources but it is assigned to a **scope**. A scope can be 
- management group
- subscription
- resource group
- single resource
The access control is hierarchical, so if you assign an access to a parent resource, it gets inherited by children resources.
Azure role-based access control is enforced through **Azure Resource Manager** which check any action that is initiated against an azure resource.

### Zero trust model
it is a security model that assumes the worst case scenario and protects resources based on this assumption. It is based on these principles:
- verify explicitly: always authenticate and authorize
- least privilege access: limit user access to the minimum required
- assume breach: verify e2e encryption, use analytics to spot malicious actions, improve defenses.

Traditionally corporate networks were considered safe thanks to the fact that the access was restricted. With the cloud everything is in the public network.

### Defense-in-depth
Security strategy that uses a series of mechanism to slow the advance of an attack. Like a set of layers that provide additional protection.
- The physical security layer is the first line of defense to protect computing hardware in the datacenter.
- The identity and access layer controls access to infrastructure and change control.
- The perimeter layer uses distributed denial of service (DDoS) protection to filter large-scale attacks before they can cause a denial of service for users.
- The network layer limits communication between resources through segmentation and access controls.
- The compute layer secures access to virtual machines and keep systems patched.
- The application layer helps ensure that applications are secure and free of security vulnerabilities, store secrets in secure storage, design application with security in mind.
- The data layer controls access to business and customer data that you need to protect.

### Microsoft defender for Cloud
it's a monitoring tool for threat protection, it monitors cloud, on-premises and multicloud (even on other cloud)environments to provide guidance to strengthen security.
It is automatically included in azure native services, but you can deploy instances to protect on-premises datacenter.
It detects threats across PaaS services (app service, sql, storage accounts, ..), data services, networks (limits exposure to brute force attacks, limit IP ranges and access times).

Defender for cloud
- continuously access: know your situation and security posture
- secure: harden resources and services (put in place security polices) Defender for Cloud assesses if new resources are configured according to security best practices. If not, they're flagged and you get a prioritized list of recommendations for what you need to fix. Recommendations help you reduce the attack surface across each of your resources. The list of recommendations is enabled and supported by the Azure Security Benchmark. This Microsoft-authored, Azure-specific, benchmark provides a set of guidelines for security and compliance best practices. Defender for Cloud groups the recommendations into security controls and adds a secure score value to each control. The secure score gives you an at-a-glance indicator of the health of your security posture
- defend: detect and resolve threats to resources. When it detects a threat it generates a security alert describing the details of the affected resources, suggesting remedies, optionally providing a trigger in response.


