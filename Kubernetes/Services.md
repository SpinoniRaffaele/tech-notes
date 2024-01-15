# Services

in kubernetes, services are logical components that can handle interactions between different containers in the same POD, or with the POD and some external sources (i.e. DB), or with the POD and the user interaction, they are a sort of adapters.

Remember that the node has a certain IP, while the PODs inside it have different IPs, from the Node you can access the POD, but from the outside you can only access to the node.
One of the main usages of services is to forward requests from a port of the NODE to a port of the POD,
this type of service is called NODE PORT: it forwards between the TARGET PORT (of the POD) and the NODE PORT (of the node - only in the range 30000 - 32767), passing through the service's own port, called PORT.
definition file of the service:
```
apiVersion:v1
kind:    Service
metadata:   
    name: myService
    labels:   
        app: myApp
spec:   
    type: NodePort
    ports:
        - targetPort: 80
          port: 80
          nodePort: 31000
    selector:
        //put here the same labels of the POD you want to link to this (like it happens in the replica set)
```
with this setup, if you have different PODs having the same label, the service will link to all of them and provide a basic load balancing technique: RANDOM load balancing.
By default, a service spans across all the nodes in the cluster and links to all the containers in the different nodes.

LOAD BALANCERS
the NodePort service implements only the random load balancer strategy, in order to have better strategies you should run your own load balancer on a POD and expose it as the entry point of the application, another alternative is to rely on K8s supported cloud load balancer, namely, the load balancer of google cloud, AWS, azure. In one of these 3 environment you can create directly the service with the type: LoadBalancer

CLUSTER IP: it creates a virtual IP inside the cluster, to enable communication between different PODs:
imagine having a set of frontend PODs and a set of backend PODs that need to communicate.
Cluster IP is a layer is between, it has a name and an IP and it is the communication bridge between the different types of PODs
```
apiVersion:v1 
kind:    Service
metadata:     
    name: myService 
    labels:     
        app: myApp 
spec:   
    type: ClusterIP
    ports:
        - targetPort: 80  //the port of the PODs involved
          port: 80  //the port of the service, notice that there is no nodePort
    selector:
        //put here the same labels of the POD you want to link to this (like it happens in the replica set)
```
