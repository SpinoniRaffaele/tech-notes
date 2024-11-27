# Kubernetes + pod + replicaset+ deployment

is a cloud based deployment application
**is a container orchestration technology developed by Google**
container: [[Book by Nigel Poulton]]
orchestration: if you want your application to use a variable amount of containers based on the load you will need an underlying environment in which those containers are executed, which is configurable and can scale the instances
this can also make your application more reliable because you can run multiple instances of it, and if one fails the others are able to serve other clients.

A **Node (Worker Node)** is a virtual or physical machine that runs our containers. is a machine where kubernetes runs
A **Cluster** is a set of nodes (could also be just one node) grouped toghether (more reliability, sharing load).
A cluster contains a **Master** which is a special node orchestrating the other nodes in the cluster

Inside a worker node you have the **container runtime** which generally is Docker (it is the place where the containers are instantiated)

Inside the master node you have the **kube-apiServer** running (which is an API communicating with the other nodes).
the apiServer is present only on master nodes, white the worker nodes contains the **kubelet** (which is the component communicating with the apiServer in the master).
The master also has a **key value store (etcd)** in which it stores info regarding the other nodes
finally the master node runs also other orchestrating processes:

* controller
* scheduler

link: https://startkubernetes.com/blog/k8s\_master\_and\_worker\_nodes

In order to create a K8 cluster you can rely on AWS/GCP/Azure, or you can use admin tools like Minkube (toghether with kubectl and virtualbox) to create a local node on your machine

The node are executing container not directly, but throught **POD** which are the smallest components in kubernetes, each POD runs one container (1 POD - 1 container, in general), in order to scale a node can increase the amount of pods
The POD is configurable in order to have a shared storage, runtime, between different containers contained in a POD.

```
kubectl run PODNAME --image=IMAGENAME
```
this will instantiate a POD and pull the IMAGENAME from the dockerhub, and run it inside the pod.

Each POD get its own IP address, you can see it running:
```
kubectl get pods -o wide
```

a POD is defined in a YAML file with this structure

```
apiVersion:  //v1, apps/v1, this represent the version of the API of K8s
kind:    //Pod, Service, ReplicaSet, Deployment
metadata:    //any kind of metadata
    name: myPod
    labels:    //under labels you can put any kind of key/value
        app: myApp
spec:    //this is different for each object kind
    containers:    //the list of containers runnning in the POD
        - name: nginx-container 
          image: nginx
          ports:  //facultative specification of a port where to look for the running container
              - containerPort: 80
          env:  //facultative section on environment variables
            - name: password
              value: "admin123"  //clearly this information should be stored in SECRETS and not here
            - name: username
              value: "postgresUser"
```
you can install the VSCode extension YAML (from red hat) then open the setting of the extension and change the schema validation adding in the JSON configuration file:
```
{"yaml.schemas": {"kuberetes": "*.yaml"}}
```
this works suggesting you the fields only after the first line is written (apiVersion: v1)

The r**eplication controller** handles the replication of multiple istances of the same POD inside a cluster (the replication controller spans across multiple nodes), even with a single POD the replication controller will insure that 1 POD will always run. A new technology doing the **same thing** is called REPLICA SET.

it can be created starting from the YAML as the POD:
```
apiVersion:  v1
kind:    ReplicationController
metadata:   
    name: myReplicaSet
    labels:   
        app: myApp
spec: 
    template:  //under template put the YAML template of the PODs you want to control, there is an example here   
        metadata: 
            name: myPod
            labels:
                app: myApp
        spec:
            containers: 
                - name: nginx-container   
                  image: nginx   
    replicas: 3    //specify the wanted amount of replicas


```
then you can create the replication controller by running:
**kubectl create -f FILENAME.yml**
and get info about it with: kubectl get replicationController

For the replica set:
```
apiVersion:  apps/v1
kind:    ReplicaSet
metadata:   
    name: myReplicationController
    labels:   
        app: myApp
spec:   
    template:  //under template put the YAML template of the PODs you want to control, there is an example here     
        metadata: 
            name: myPod
            labels: 
                app: myApp
        spec:
            containers: 
                - name: nginx-container   
                  image: nginx   
    replicas: 3    //specify the wanted amount of replicas
    selector:
        matchLabels:
            app: myApp    //it allows to include different PODs in the set, based on the label (not on the POD template)
```
the matchLabels functionality of the replica set is a way of including already existing pods inside a new replica set.
How to change the number of replicas?

* update the YAML file and then run **kubectl replace -f FILENAME.yml**

or

* run **kubectl scale --replica=NEW\_VALUE -f FILENAME.yml**

**DEPLOYMENTS**
Whenever you update or rollback, you want to apply these operations to all the running instances.
we would also like to pause and resume the deployment of new changes (freeze deployments)
Deployments are wrapper of the replication controller /replica set, they add deployment functionality to the set of PODs.

The deployments are created starting from a yml file, its content is equal to the one of the replica set, the only thing changing is: **kind: Deployment**

Rollout: is the command that rolls out a new deployment, any rollout will create two revisions, one is the status before the update, the other is the status after the update.
there are 2 deployment strategy:

* **recreate**: destroys the older version, and then creates the new ones (it will cause a timewindow of unavailability) - not defualt
* **rolling update**: destroy one instance of the old app and brings up one instance of the new app, one instance at a time - DEFAULT

the deloyment component perform a deployment by creating a new replica set that has the same components has the old one (exept from the version of the runned application or image)

**kubectl apply -f DEPLOYMENT-YAML-FILE-NAME**   //to perform a new deployment, you should have changed the image in the file to then deploy the new one (otherwise use **kubectl edit deployment DEPLOYMENT NAME**)
**kubectl rollout undo deployment/DEPLOYMENT-NAME**   //to perform a rollback
**kubectl rollout history deployment/DEPLOYMENT-NAME**   //to check the history of deployments

**NETWORKING**
in kubernetes each POD gets its own internal IP address, new PODs will have new IP, the PODs deployed in multiple nodes could have the same IP, leading to networking problems, there are plugins that handles these problems, such as:
**cisco ACI networks, flanner, calico**
note that you don't need any of this if you have only one node.
