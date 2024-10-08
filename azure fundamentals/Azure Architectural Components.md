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

**Region Pairs**: most Azure regions are paired with another region at lease 300 miles away. This allow for replication of resources in a region pair, in order to be resilient to massive issues that impact an entire region. (ex: West US + East US).

Sovereign Regions: Instances of azure that are isolated from the main instance of Azure (used by governments, ex: US Gov Virginia, China East)

### Management Infrastructure
A **resource** is the basic building block for azure, anything you create, provision, deploy, etc... is a resource.
**Resource group**: a group of resources, each resource must be in a unique resource group, resource groups cannot be nested and the resources can be moved from group A to group B.
When you perform an action on a resource group you are doing the same action for all the resources inside.

**Subscription**: unit of management, billing and scale. They allow to logically group resource groups to facilitate billing and management. You can implement boundaries between subscriptions:
- Billing boundaries (different way of billing resources)
- Access Control boundaries
You can use subscriptions to separate environment (testing and prod), or different structures inside the organization, or different billing purposes (so you can easily track costs in grouped sections).

**Management group**: management level above subscriptions, used to apply common governance conditions to the different subs. Used for enterprise-grade management. Management group can also be nested. Management groups also apply role-based access control that will be inherited by all the entities underneath. 10000 management groups are the limit for a single directory, the nesting can reach up to 6 levels.

## Azure virtual Machines
They are a type of IaaS, it is a virtualized server that can be user in any way with any software, you have total control over the OS, the hosting configuration and the software inside. You can create VM from an array of predefined image to have an OS ready in minutes. When choosing a VM you need to specify the resources associated: size (cores and RAM), storage disks, networking (public IP addresses, v net, port configuration)

**VM Scale Sets**: this azure component allow you to manage, configure, and update a set of VMs together in one shot, you can set up scalability to increase/decrease the amount of machines in the set. Under the hood the scale sets deploys a load balancer that makes sure to balance the load across the VMs.

**VM Availability sets**: an azure component that uses varied pawer and network connectivity to various VMs to have a logical reliable unit. It accomplishes this in two ways:
- Update domain: is a grouping of VMs that can be updated at the same time. An availability set spans across multiple update domain allow to apply rolling update so that only one VM at a time is down.
- Fault Domain: a group of VM with common power source and network switch. An availability set spans across multiple fault domain.
Availability sets are free and you pay only for the VM instances that you create.

VMs are also an excellent choice when you move from a physical server to the cloud (also known as lift and shift). You can create an image of the physical server and host it within a VM with little or no changes. Just like a physical on-premises server, you must maintain the VM: you’re responsible for maintaining the installed OS and software.

Azure Virtual Desktop: types of VM, it enables the usage of a cloud-hosted OS from a virtual remote control (it works with many apps that offers remote desktop solutions, or even in the browser) With Azure virtual desktop, users connect to the remote VM in a secure v net, the manager of the resource can decide how many user can connect to it at the same time.
You can scale up and down as needed. the app will work and look like it's local (except for a small icon identifying that is it is virtual). This enhances security because it's centralized and the users don't have to worry. The data and the apps are separated from the client's hardware making the risk of confidential data leak smaller.