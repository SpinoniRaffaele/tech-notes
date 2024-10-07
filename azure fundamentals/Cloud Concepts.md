Cloud computing is the delivery of computing services over the internet (VM - compute power, storage, DB, networking, IoT, ML, AI).
It is globally distributed and not physically constrained.
With the cloud you only pay for the resources that you use, and the infrastructure layer is managed by the provider (updates and redundancy)
You don't pay for the physical infrastructure, electricity, ..
You pay for the cloud resources that you use
### Shared Responsibility model
when using a cloud provider some reponsabilities will fall in their hands: physical security, power, cooling, network connectivity.
The client is responsible for the data stored and the access security.
The responsability shift from the client to the provider based on the type of **service type** used:
- **IaaS**: infrastructure, the provider is responsible only for the physical security, power, connectivity. With iaas the client has the maximum flexibility, only the hardware is handled by the provider. (Commnly used for cloud migration 1 to 1, rapid testing of PoCs)
- **PaaS**: platform, a bit of network control, a bit of application management and the operating system handling and patching are handled by the provider. The providers maintains also development tools and business intelligence services. (this is the general solution for development environment)
- **SaaS**: software, application management, network control and identity management infrastructure are responsablities of the provider. It is the most complete cloud solution, it's like renting a fully developed application. Commonly used for support systems across the company: email, messaging, expense tracking application.
![[shared-responsibility-b3829bfe.svg]]

### Cloud Models
- Private cloud: it's a cloud that us used by a single entity (the private cloud of a corporation) it requires greater costs.
- Public cloud: built, controlled, maintained by a third-party provider.
- Hybrid cloud: a mix of the two with a bit of private infrastructure being completed by a public cloud infrastructure.
- Multi cloud: using a set of different cloud providers

Azure arc are a set of technologies that helps you handle your cloud environment.

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
	- **Management of the cloud**: you can easily scale resources, deploy and remove them, monitor health and replace failing resources, receive automatic alerts based on confiugured metrics.
	- **Management in the cloud**: you can manage the cloud through the web portal, using CLI using API, using PowerShell.
