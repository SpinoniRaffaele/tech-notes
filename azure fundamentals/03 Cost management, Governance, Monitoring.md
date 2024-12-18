## Cost Management
Azure cost is impacted by: 
- resource type: (when you provision a resource, azure creates metered instances that track resource usage and compute your bill), the region, resource dimension and other resource settings have impact on the cost
- consumption: cost proportional to usage
- maintenance: maintain what you need and remove what you don't
- geography: the cost of power labor and taxes change depending on the region.
- network traffic: different cost for inbound/outbound traffic, generally the inbound traffic is free
- subscription type: in case of free trial subscription
- marketplace items: when buying a resource from the marketplace you pay both azure for the resource and the third-party vendor for their services/expertise

Cost reducing factors:
- reservation: you can set an amount of consumption time in advance to receive discount (1 year / 3 years). You can reserve instances or capacity (for Db for ex)
- hybrid use benefits: you can use the licenses that you already have on the azure resources that you provision avoiding the extra cost of the license in the azure resource.
- spot VM: you can use the VMs that are tagged as 'unreserved' by azure (those are used by azure as a buffer in case they have scaling needs), they are cheaper but they can be revoke at any time by azure.

Azure has many tools to help you understand the cost of your azure resources:
**Total cost of ownership (TCO) calculator:** Used to compare costs between on-premises and azure resources. You can fill the details of your on-premise configuration, with IT labor cost, servers, databases, traffic, ...
**Pricing calculator**: It gives you an estimated cost for provisioning a resource in Azure. useful to estimate future costs for a solution.
**Azure advisor**: recommend ways to optimize the costs and monitor unused resources

**Cost Management**: is a tool in the azure portal that provides the ability to check resource cost, create alerts based on the spendings (budget alerts, credit alerts, department spending quote alerts), create budgets (set a limit for azure, based on subscription, resource group or others. You can configure automation that suspends resources upon budget alerts). You can visualize the costs in different ways (by region, by resource, ...)

**Tags**: a way to organize resources, they provide key value metadata to the resource. Useful for:
- resource management
- cost management
- operations management (you can group resources based on how critical they are)
- security (group by security level)
- governance and regulatory compliance
- workload optimization and automation (associate resources to workloads).
Resources don't inherit tags from resources groups or subscription

## Governance and Compliance
**Microsoft Purview**: family of data governance, risk and compliance solutions that helps you handle your data. It has automatic data discovery and sensitive data classification to stay up to date with your generated data.
It helps protect sensitive data, identify and manage regulatory compliance requirements.
The unified data governance solutions manage all your data: on-premise, on azure and even on other clouds. It creates up-to-date map of your data estate, generate insights about how your data is stored and used, manage access.

**Azure Policy**: is a service in azure that enables you to create, assign and manage policies that control your resources. You can define standards and prevent the creation of resources that are not compliant.
You can define policies at each level: subscription, resource group, resource. They will be inherited by child resources. In some cases Azure can automatically change the resources being created to adapt them to a certain policy (like adding a specific tag), you can of course remove this behavior at resource level.
Azure policy initiative: a way of grouping related policies together. 
Azure policy comes with predefined policies and initiatives

**Resource Lock**
A mechanism that prevents resources from being deleted or changed, it can be applied to resources, resource groups or subscription (not on management group level). They are inherited.
- Delete lock: it's still possible to modify the resource
- readOnly lock: no changes allowed
These locks cannot be overridden, even the owner of the resource must delete the lock to be able to delete the resource.

**Service Trust Portal**: a portal that provide access to content about security, privacy and compliance practices. [https://servicetrust.microsoft.com/](https://servicetrust.microsoft.com/) you can access with a Microsoft account.

## Manage and Deploy Resources
Azure provide multiple tools to manage resources:
- **Azure portal**: web based graphical unified platform, it also allow you to configure custom dashboard and has accessibility options. It has high availability since it is present in every azure datacenter; it is constantly updated. Inside the portal you have access to the **Cloud shel**l: a browser based shell, fully free, it doesn't require local installation and supports both powershell and bash.
- **Azure powershell**: shell exposing commands to devops, these commands call the Azure REST API to perform resource management. With scripts, the process of changing/creating resources become tracked, repeatable and automatable
- **Azure command line interface** (CLI): equivalent to the powershell it is using bash syntax

**Azure Arc**: a centralized and unified way to:
- manage your environment (manages both azure and non-azure resources, even on-premises)
- manage multi-cloud VM, K8 clusters and databases as if they were running in azure

The resources in azure are managed by the **Azure Resource Manager (ARM)**, arc is built on top of ARM. It is currently limited to: servers, kubernetes clusters, azure data services, sql server, VM.
ARM is the deployment and management service for azure. when you manage azure resources from any interface, the request are sent to ARM which authenticate and authorize the requests and dispatches them to the azure service.
With ARM you can manage the infrastructure through declarative templates, manage resources at once as a single group, define dependencies between resources to deploy them in correct order, apply tags and access control.

**Infrastructure as code (IaC)**: the principle of managing your infrastructure with lines of code, thanks to **ARM templates**, you can automatically manage resources by providing a template containing the expected resources and their configuration in a declarative JSON format.
Using ARM templates has multiple benefits:
- declarative syntax.
- repeatable results.
- orchestration: you don't have to worry about the ordering of operations, azure orchestrate the deployment in correct order if dependencies are specified.
- modular files: you can break down your infrastructure in multiple files.
- extensibility: the templates can be extended with custom scripts (powershell or bash).

**Bicep**: a language that uses declarative syntax to deploy azure resources. It is a concise alternative to the JSON ARM templates. It supports all resources types, it has a simple syntax and offers the same advantages as ARM templates do.

## Monitoring
**Azure Advisor**: is a tool that continuously scan and evaluates the resources and makes recommendations to help improve **reliability**, **security** and **performance**, achieve **operational excellence** and reduce **costs**.
You can filter the suggestions by resource groups or subscriptions. It is a free service.

**Azure Service Health**: a service that keeps track of the resources (both your specific ones and azure in general). It uses three different services:
- Azure status: the status of azure globally (used to monitor outages in AZs).
- Service Health: it focuses on azure service and regions that you use, you can set it up to send you alert when certain conditions are met.
- Resource health: provides info about a single resource.

**Azure Monitor**
it is a platform for collecting data on your resources, analyze them, virtualize the information and even respond with actions. It can monitor azure, on-premises and even multi-cloud resources.

Azure monitor uses central repositories to store data about metrics, logs and traces.
The data is then accessible in Azure monitor in several ways: graph historical datapoints, create reports, create custom views with Power BI, use it to react in real-time (like autoscaling when a threshold is reach). You can set up alerts to be informed when a threshold is being crossed

**Azure Log analytics**: a tool where you can analyze logs, query them, filter, visualize results.

You can go deeper in gathering data by installing **Application insights SDK** in your application that run in the cloud resources, and then in azure you can monitor a broader set of information:
- requests rates, times.
- dependencies
- page view and load performance
- users and session counts
- performance counters for Windows or Linux VMs.
