Kubernetes is a cloud-native orchestrator of containeraized microservices applications.

Kubernetes is a cluster, and like other cluster it is made of nodes; Some nodes a part or the control plane which controls the other worker nodes where applications run (data plane). The master nodes are in charge of the cluster, they make scheduling decisions, monitor, implement changes, respond to events. In production environments there are multiple nodes acting as master to ensure high availability.
Components of the master nodes:
- API Server: ensures all communication with all the components. It defines also an interface with which the cluster communicate. It exposes a REST API that travels over HTTPS.
- Cluster store: the only stateful part of the cluster, it stores the desired and actual state of the components. It is based on etcd, it's important to have multiple instance of it in multiple master nodes.
- Controller manager: implement the control loops that monitor the cluster and respond to events. It spawns the independent control loops and monitors them. 
- Scheduler: it watches the API for new tasks and assigns them to appropriate healthy nodes based on the fact that the node is able to execute the task and it also ranks the nodes based on their resources to understand the best one to execute a tesk.
- Cloud Controller manager: in case you run kubernetes in the cloud (Azure, GCP, IBM Cloud, ..) the control plane will be running the cloud controllermanager that handles the integration of kubernetes with the other cloud components.

Worker nodes are simpler: they watch the API for new tasks, execute tasks, report events. They are made of:
- kubelet: the kubernetes agent that act as the node's kubernetes agent.
- container runtime: kubernetes doesn't directly run application, is the container runtime (containerd) that pulls and execute images.
- kube proxy: it is responsible for the cluster networking.

Outside of the nodes, kubernetes runs an internal DNS service: it is in a static IP which hard-coded in to every pod in the cluster, every service is registered in the DNS so that every POD can communicate with it.

Kubernetes orchestrate the application declaratively. You don't describe the steps required to reach a goal (as in imperative style) but you describe the desired final state of your system using a set of YAML files. You POST those files to Kubernetes and it will take care of making it happen. This allow for repetable results and for IaC where the infrastructure of your cluster is versioned as code. If something goes off track and it is not anymore aligned with the desired state, kubernetes tries to fix the situation (self - healing).

Kubectl is the main CLI tool for k8s, it's like SSH for k8s, available on the major OSs, it converts user friendly commands to JSON payload to the API server. in the .kube directory there is a config file containing definitions for:
- clusters: list of clusters the kubectl knows about
- users: different users and permissions over the cluster
- context: context are a set of cluster and users.

`kubectl describe RESOURCENAME` is a powerful command to get info about k8s resources
`kubectl logs PODNAME --container CONTAINERNAME` get the logs of a container (the first by default) inside the specified pod

# POD
You write an app, you conteinerize it and store it in a docker registry, you define a Kubernetes pod that target your image and you deploy the pod using high level controllers like 'deployment' or 'replica set'.

The pod is the unit of execution of kubernetes. It can contain multiple containers (but generally is better to decouple them). The deployment of a pod is an atomic operation that can only fail or succeed. The POD is an environment that runs containers, it's the host for the container, it ring-fence an area of the OS, build a network stack, create kernel namespaces and run containers in it. If you run multiple containers in the same pod they will share all of this (they share the IP for example and they can refer to each other using localhost, they share ports, sockets, memory, volumes and more).

A pod is a fancy name for a type of containers, which means that a pod is a container of containers.

Application centric use-case for multi-container pod: co-scheduling tightly-coupled workloads such as a two containers that share memory.
Infrastructure centric use-case for multi-container pods: service mesh, such as a logging container next to a webserver, or an intelligent router, or traffic encrypter.

PODs are mortal and their IP changes every time they are redeployed or updated like their ID. This means that the applications have to be designed to avoid coupling between pods IP or ID, if you need inter POD communication you will use Services. 

The POD lifecycle starts from the moment you post the yaml of it, the pod is scheduled to a healthy node and it enters the PENDING state while the container runtime download the image and start the containers. Once everything is up and running the pod enters the RUNNING state (or FAILED state) and when it terminates it reaches the SUCCEEDED state.

The pod yaml contains:
- apiVersion: version of the kubernetes API in the format apiGroup/apiVersion (ex: storage.k8s.io/v1), but the apiGroup cam be omitted for the core elements like a pod.
- kind: the kubernetes object
- metadata: labels and name
- spec: info about the containers inside the pod

## Deployment
A deployer is another kubernetes component, it is a higher level wrapper around a POD, it can handle only one pod template but multiple instances of it. Under the hood each deployment uses a replica set to handle the set of pods, each new version of the pod will stay in its own replica set (this eases rollbacks). With deployment you gain scalability, self healing, rolling-updates and rollbacks. When you define the specs of a deployment you add the number of replicas that you want for a POD. The Replica set works a reconciliation loop that ensures that the actual replicas of a pod matches the desired number of replicas. I foyu want to permform an update you change the yaml file of the deployment targeting the new image and you re-POST the yaml to the API server and recording to the deployment history.
(`kubectl apply -f deploy.yaml --record=true`)
. To deploy the new version kubernetes will create a new replica set and, one by one, change each pod to target the new one (rolling update with zero downtime). After all the pods are updated, the old replica set is maintained there, empty, in case of rollback.

example of deployment specs:
```yaml
spec:
  replicas: 10
  selector: 
    matchLabels: 
      app: myapplabel
  minReadySeconds: 10
  strategy:
    type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 1  //this means that while updating we can have 10 - 1 replicas
    maxSurge: 1  //this means that while updating we can have 10 + 1 replicas
  template:
    metadata:
      labels:
        app: myapplabel
	spec:
	  containers:
	  - name: myapp
```

you can check the history of a deployment with:
`kubectl rollout history deployment DEPLOYMENTNAME`

For quick rollbacks you can do with a kubectl command, but this is not recommended, i would be better to deploy the old version of the application, this because the reollback is an imperative command that does not fit in the declarative philosophy of kubernetes.
ex: `kubectl rollout undo deployment DEPLOYMENTNAME --to-revision=VERSIONNUMBER`


# Service
Services are kubernetes components that provide a stable DNS name and IP address as a front-end for a set of POD that change dynamically. The service performs also load balancing and automatically updates itself when POD changes. Service work at TCP/UDP level, so if you need application level routing you will need **Ingresses** . The PODs inlcuded in a Service are determined by a label selector that is a list of all the labels a POD must posses to receive traffic from a service. The logic to loosely couple PODs with services is an AND over the labels specified in the label selector. Under the hood, a service uses an Endpoint object that is a dynamic list of all the healthy pods that match the label selectors.
There are many ports involved in a service:
 - port: configures the port on which the service listen for cluster-internal requests
 - nodePort: configures the port on which the service listen for external requests
 - pargetPort: the port to use when communicating in the backend with the pods.
### ClusterIP
Is a type of Service that is accessible only from whithin the cluster. The service IP and port are registered in the cluster DNS that is hardcoded and accessible from every node, so everyone in the cluster knows about the service.

### NodePort
It is built on top of ClusterIP and it enable access from outside the cluster with a NodePort exposed outside the cluster that maps to one of the service internal ports. node port values are by default between 30000 and 32767

### LoadBalancer
Is a service that integrates with load balancers of cloud providers. It is built on top of NodePort

### ExternalName
A service that routes traffic to systems outside of your kubernetes cluster, generally not used.

## Service Discovery
Service registration is the process of a microservice registering its connection details in a **service registry** so that other microservices can find it.
Kubernetes uses a DNS as service registry, services registers with the DNS. Every container has the DNS IP because kubernetes populates the `/etc/resolv.conf` file with the IP address of the cluster DNS. 
Service Discovery is the process of finding the microservice with which we communicate.
Any pod can refer to the service name to communicate with the pods behind, the DNS will resolve the service name and reach the service.
In details, the IP address of the services is on a special network not accessible from the container. It will default to the default gateway (the node); the node itself doesn't have the route to the service IP and it will default to the default gateway (Node kernel). The node kernel contains trap IPVS rules that are added by the kube proxy service by listening to changes in the endpoint objects (through the API server). These rules intercept traffic of the node kernel redirecting them to the service.

### Namespace
Namespaces are a partition of the network address space of a cluster. They are useful for creating different environments with different quotas, however they are not ISOLATION BOUNDARIES and should not be used to isolate hostile loads. This because the DNS is general for the cluster and shared across namespaces, this means that any microservice in a namespace can resolve microservices in other namespaces by using fully qualified names that include also the namespace name. 

# Storage
Kubernetes ships a persistent volume subsystem to handle storage. Kubernetes doesn't handle any storage directly but it plugs in with external storage solutions.
You have kubernetes component called Persistent Volumes (PV) which are the k8 representation of an external storage solution.
Persistent Volume Claims that are tickets that allow pods to use a PV.
Storage Classes: component that manages PV: you generally define the specs for storage classes which in turn automatically handle PVs.

### Persistent Volume
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: demo-pv
spec:
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 1Gi
  storageClassName: standard
```
Access modes describe how the PV can be accessed:
- ReadWriteOnce: can be used only by one PVC
- ReadWriteMany: can be used by many PVCs
- ReadOnlyMany: can be used by many PVCs in readonly

The capacity can be less than the max capacity of the actual storage solution

### Persistent Volume claim
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-dynamic
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: standard
```
It matches the PV by name and it must have the same spec section


Any pod can then refer the persistent volume claim:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pvc-pod
spec:
  containers: ...
  volumes:
    - name: data
      persistentVolumeClaim:
        claimName: pvc-dynamic
```

### Storage Class
They are immutable objects in the kubernetes API. It describes how PV should be created by K8.
Once deployed the storage class watches the API server for new PVC, when they appear, it creates dynamically a PV and the corresponding volume on the backend.
```yaml
apiVersion: storage.k8s.io/v1  
kind: StorageClass  
metadata:  
	name: standard  
	provisioner: kubernetes.io/aws-ebs  
	parameters:  # these parameters are specific to the provisioner (which is the plugin to the specific storage)
		type: gp2  
		fsType: ext4  
		reclaimPolicy: Delete  
		volumeBindingMode: Immediate  
```

The typical workflow for the storage solution in your k8s cluster is
- ensure the plugin needed exists
- create a storage class
- create a persistent volume claim
- deploy a pod that uses the persistem volume claim

# Config Maps
In the pas application binaries and their configurations were bundled toghether, now moving towards the cloud microservices applications, this is an anti-pattern.
By decoupling application and configuration we gain:
- re-usable application images in different envs
- simpler testing
- fewer disruptive changes

You should have only one copy of your app that you can deploy in different environment with different configurations applied to it.
Kubernetes provides you the **ConfigMap** to store configuration data outside of your pods. It's important to avoid storing sensitive datsa in configmaps and use **secrets** instead.
Config maps are simply a map of key value pairs, where keys are strings and value can contain anything (also entire file content).
Data in a config map can be injected in a pod at runtime in different ways:
- environment variables
- arguments to the container startup command (still env variables)
- files in a volume attached to the container

```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: multimap
data:
  given: nigel
  family: Poulton
```

### Config Maps as env variables
After creating the configmap you can map its entries into environment variables in the container section of the pod.
A drowback of doing this is that configmaps are static meaning that if you change the value it won't be reflected in the container.

![[configmapsk8.png]]

### Config Maps with volumes
This is the most flexible options as when the values are updated in a config map, their new value are stored in the volume making them dynamic.
This stores one file for each config map key.

In order to have this setup you need to:
- create a config map
- create the config map volume in the pod template and mount it:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cmvol
spec:
  volumes:                 # this section creates a ConfigMap volume
    - name: volmap
      configMap:
        name: multimap
  containers:
    - name: ctr
      image: nginx
      volumeMounts:          # specify where the configmaps is mounted
        - name: volmap
          mountPath: /etc/name
```
# Stateful Sets
Stateful application: an application that creates and saves persistent data.
Stateful sets are used to manage such applications, they are comparable to Deployments.
Stateful sets are part of the core api and they use controllers that run reconciliation loop to make sure that the state of the cluster match the declared one. They support self-healing, scaling and updates.
On top of this (which is true also for deployments), they offer extra guarantee:
- predictable and persistent Pod names
- predictable and persistent hostnames
- predictable and persistent volume bindings

Those are stable upon failures, scaling and other scheduling operations.

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
	name: tkb-sts
spec:
	selector:
		matchLabels:
			app: mongo
	serviceName: "tkb-sts"
	replicas: 3
	template:
		metadata:
			labels:
				app: mongo
		spec:
			containers:
			- name: ctr-mongo
		      image: mongo:latest
```

The format of the predictable names of PODs in a stateful set is 
`{statefulSetName}-{integer}`  with the integer being a 0-based number.
Stateful sets create one pod at a time, they wait for the previous pod to be live and ready before starting the creation of the new one (While deployments would create them all at the same time causing potential race conditions). This is applied also when you scale up or down (one pod at a time will be added/removed). But deleting a stateful set won't terminate the pod in order.
This behavios is the default, but it can be turned of by specifying a property inside the spec section of the stateful set:
`podManagementPolicy: Parallel`

When performing rolling updates, the stateful set always starts with the highest numbered pod and works down through the set, updating the pod container's one by one.
Also, stateful sets do their own self-healing and scaling without the need of a replica set under the hood (unlike deployments).

Stateful sets also manage volumes, they are decoupled from pods with the usual constructs (Persistent Volumes and Persistent Volume Claims), they are named in a predictable way to connect them to the right pod.

Being pod and volumes decoupled, if a pod fails, the volume won't and it will be ready to be plugged to the new pod substituting the failed one.
![[statefulsets volumes.png]]


Each stateful set's pod needs its own unique storage, so also a unique PVC, but this isn't possible beacuse you would need to create a specific PVC for each pod in the storage set and keep adding and removing PVCs after scaling up or down the pods.
To solve this, stateful sets use Volume Claim Template. Those objects (specified in the stateful set `spec` section) dynamically create
new PVC and name them in a consistent manner. So whenever we scale the Stateful set we actually scale the pods and the PVCs; the volumes and the PVCs will only scale up and not down to avoid potential data loss, future scale up operation of pods will result in connecting new pods to the already created PVCs and Volumes.
Example of  `volumeClaimTemplate` section:
```yaml
volumeClaimTemplates:
- metadata:
	name: webroot
  spec:
	accessModes: [ "ReadWriteOnce" ]
	storageClassName: "flash"
	resources:
	  requests:
		storage: 1G
```

### Headless Service
Since stateful sets create predictable and long lived POD, it is possible to connect directly to the specific POD instance using a **Headless Service**:

```yaml
apiVersion: v1
kind: Service
metadata:
	name: mongo-prod
spec:
	clusterIP: None
	selector:
		app: mongo
		env: prod
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
	name: sts-mongo
spec:
	serviceName: mongo-prod     # association to the headless service
```

An headless service is a service object with a clusterIP set to `none` (it's like a service without the frontend ip to connect to it).
It becomes a **Governing Service** when you list it in the stateful set manifest.
In this scenario, the service creates DNS records for each pod in the stateful set that matches the label selector of the service.
Other PODs can refer to the statefulset's pod's names to communicate with them.