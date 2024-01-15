# Kubectl

is the command line utility used to interact with a node running kubernetes,

some commands:

* run: deploy an application in the cluster
* run PODNAME --image=IMAGENAME: deploy a pod with a specific image pulled from docker
* cluster-info  return information about the whole cluster managed by the master
* get nodes: list the nodes that are part of the cluster
* get pods: list the pods contained in the cluster
* create -f CONFIGURATION.yaml
* describe pods PODNAME : or also with nodes, it describe things
* edit pod PODNAME
* get replicasets
* get all (gets every type of K8s object)
* delete replicaets NAME
* replace -f FILENAME.yml    //edit an object, for example POD or replicaset, or controller, or service
* kubectl apply -f DEPLOYMENT-YAML-FILE-NAME   //to perform a new deployment, you should have changed the image in the deployment definition file to then deploy the new one
* kubectl rollout undo deployment/DEPLOYMENT-NAME   //to perform a rollback
* kubectl rollout history deployment/DEPLOYMENT-NAME   //to check the history of deployments
