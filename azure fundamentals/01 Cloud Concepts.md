Cloud computing is the delivery of computing services over the internet (VM - compute power, storage, DB, networking, IoT, ML, AI).
It is globally distributed and not physically constrained.
With the cloud you only pay for the resources that you use, and the infrastructure layer is managed by the provider (updates and redundancy)
You don't pay for the physical infrastructure, electricity, ..
### Shared Responsibility model
when using a cloud provider some reponsibilities will fall in their hands: physical security, power, cooling, network connectivity.
The client is responsible for the data stored and the access security.
The responsibility shift from the client to the provider based on the type of **service type** used:
- **IaaS**: infrastructure, the provider is responsible only for the physical security, power, connectivity. With iaas the client has the maximum flexibility, only the hardware is handled by the provider. (Commonly used for cloud migration 1 to 1, rapid testing of PoCs)
- **PaaS**: platform, a bit of network control, a bit of application management and the operating system handling and patching are handled by the provider. The providers maintains also development tools and business intelligence services. (this is the general solution for development environment)
- **SaaS**: software, application management, network control and identity management infrastructure are responsablities of the provider. It is the most complete cloud solution, it's like renting a fully developed application. Commonly used for support systems across the company: email, messaging, expense tracking application.
![shared-responsibility-b3829bfe.svg](../_resources/shared-responsibility-b3829bfe.svg)

### Cloud Models
- Private cloud: it's a cloud that is used by a single entity (the private cloud of a corporation) it requires greater overhead costs. the org is responsible for the datacenter, only the org can access it. It's more secure because you can put it behind a completely private net.
- Public cloud: built, controlled, maintained by a third-party provider.
- Hybrid cloud: a mix of the two with a bit of private infrastructure being completed by a public cloud infrastructure. (org determine where to run apps, they control security, compliance and legal requirements).
- Multi cloud: using a set of different cloud providers
### Consumption-based model
CapEx (capital expenditure): one time, up-front expenditure to buy a resource (such as a server)
OpEx (operation expenditure): spendings over time for services, such as cloud services.

OpEx cloud infrastructure has benefits:
- no upfront costs
- no need to purchase infrastructure
- pay only when its needed
- stop paying when not needed anymore

To put it another way, cloud computing is a way to rent compute power and storage from someone else’s datacenter. 

### Benefits
- **Availability**: ensure that the system is available (UP) the maximum of the time. Provided by the SLA (standard level agreement) as uptime percentage (99, 99.9, 99.99)
- **Scalability**: the ability to adjust resources to meet demand. it means that if the demand drops off, you reduce the resources used and the price you pay. Vertical scaling refers to augment the capabilities of a resource while the horizontal scaling means that you add resources increasing redundancy (scaling out).
- **Reliability**: the ability of a system to recover from failures. Being the cloud decentralized over the world, it is a system with high reliability that can shift demand globally.
- **Predictability**: 
	- **Performance predictability**: ability to predict the resources needed to deliver positive experience to customers (supported by cloud mechanism such as autoscaling and load balancing)
	- **Cost predictability**: ability to predict the cost of cloud expenditure. You can track the resources in real time, monitor them, apply analytics to find patterns that ensure better deployments
- **Security and governance**: both aspects are well supported by cloud services which ensure governance of the data and compliance to government requirements
- **Manageability**:
	- **Management of the cloud**: you can easily scale resources, deploy and remove them, monitor health and replace failing resources, receive automatic alerts based on configured metrics.
	- **Management in the cloud**: you can manage the cloud through the web portal, using CLI using API, using PowerShell.


### Azure resources overview
Azure provides more than 100 services that enable you to do everything from running your existing applications on virtual machines to exploring new software paradigms, a cloud is not just a set of VMs but other tools ready to use and integrated in the environment.

In order to use Azure services you need an Azure **subscription**, with an **Azure account** one subscription is automatically created, but multiple ones can be added. Inside each subscription there can be multiple **resource groups** where different azure services are grouped.
Each subscription has its own invoice, and it's total amount to bill for the resources used
Azure offers a free account with access to 20 Azure services for 1 year, a credit to be used in the year and access to some developer tools.
Azure CLI can be accessed using `az` , for example you can start an interactive shell that helps you to insert azure commands like so:
`az interactive`, then you can type `exit`.

### Physical Infrastructure
Azure is made of datacenters, which are facilities of resources spread around the world. As a customer you don't access datacenter directly but regions and availability zones:
- Region: geographical area that contains at least one or more datacenters that work in a low-latency network, it is the LOCATION of services (although some services are global and not deployed to a specific region)
- AZ: they are physically separate datacenters in an Azure region. Availability Zones are isolation boundaries in the sense that if a zone goes down the other continues working. Different AZ in the same region are connected through a fiber-optic network. 
  There are at least 3 AZ in each region supporting AZ.
Using resources in multiple AZs will make a resilient and more available system. AZ are primarily for VM, managed disks, load balancer and SQL databases.
In Particular Azure distinguishes:
- **zonal services**: services that are attached to a zone (VMs)
- **zone-redundant services:** automatically replicated across the AZs (SQL DB)
- **non-regional services**: services always available globally, resilient to region-wide outages.

**Region Pairs**: most Azure regions are paired with another region at lease 300 miles away. This allow for replication of resources in a region pair, in order to be resilient to massive issues that impact an entire region. (ex: West US + East US). Some services are automatically replicated. The regions pair are inside a GEOGRAPHY, they are fault tolerant, they ensure resiliency.

**Sovereign Regions**: Instances of azure that are isolated from the main instance of Azure (used by governments, ex: US Gov Virginia, China East)

### Management Infrastructure
A **resource** is the basic building block for azure, anything you create, provision, deploy, etc... is a resource.
**Resource group**: a group of resources, each resource must be in a unique resource group, resource groups cannot be nested and the resources can be moved from group A to group B.
When you perform an action on a resource group you are doing the same action for all the resources inside.

**Subscription**: unit of management, billing and scale. They allow to logically group resource groups to facilitate billing and management. You can implement boundaries between subscriptions:
- Billing boundaries (different way of billing resources)
- Access Control boundaries
You can use subscriptions to separate environment (testing and prod), or different structures inside the organization, or different billing purposes (so you can easily track costs in grouped sections). Cannot be nested.

**Management group**: management level above subscriptions, used to apply common governance conditions to the different subs. Used for enterprise-grade management. Management group can be nested. Management groups also apply role-based access control that will be inherited by all the entities underneath. 10000 management groups are the limit for a single directory, the nesting can reach up to 6 levels.