# K8s on cloud

you can maintain your own self hosted solution and handle your VMs or having a hosted solution based on cloud provider,
and use Kubernetes as a service. The way of provision and manage K8s cluster in the cloud provider differs between the various providers

* Google Kubernetes Engine: you need a google cloud account, there you can create a kubernetes cluster
	once provisioned, you can access it through a terminal in the google console. There you can configure your nodes
	
* AWS: create an Elastic Kubernetes Service (with an IAM role to be able to access it), after being created you need to add nodes inside the cluster, you need also the AWS cli to interact with the cluster on AWS you can see a command to access directly the cluster using the commands from your local terminal
* Azure Kubernetes servers: same steps as google, easy, with the integrated shell

for each provider there is a documentation page that explains how to provision it.
