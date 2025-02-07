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

# Service
Services are kubernetes components that provide a stable DNS name and IP address as a front-end for a set of POD that change dynamically. The service performs also load balancing and automatically updates itself when POD changes. Service work at TCP/UDP level, so if you need application level routing you will need **Ingresses** . The PODs incuded in a Service are determined by a label selector that is a list of all the labels a POD must posses to receive traffic from a service.
