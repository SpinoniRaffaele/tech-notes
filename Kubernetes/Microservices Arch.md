# Microservices Arch

suppose you have a multilayered architecture and you want to deploy it using kubernetes, you need to have clear a graph of layers and dependencies between them.
steps:

* create a POD/ReplicaSet/Deployment for each layer of the application
* create a service for each layer that needs to be accessed by another one, in this way a service like ClusterIP is providing an IP and a port to access the app layer. those services shouldhave the same name as the hostname that is searched by the code in the layer that are accessing it.
	example: you have a redis DB that is accessed somewhere in your app, you need to create a ClusterIP service for that
	
* create a service of type NodePort for each layer that needs to be accessible from the outside.

done! If a layer is not accessed by any other (it is only accessing others), and it is not accessed by the user
then it doesn't need any service.
