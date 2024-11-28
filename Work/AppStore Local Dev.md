Before following the readme you need to make sure you have:

- Helm installed
- Docker and Kubernetes running
- Perform a:     `docker login dockerhub.rnd.amadeus.net`
- create a myamadeus namespace in kubernetes:  `kubectl create namespace myamadeus`
- build maven, add a space after the `-P` flag in the maven command
- Install the ngnix controller: 
  `helm upgrade --install ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --create-namespace --set controller.ingressClassResource.default=true`

TRY THE DEPLOYMENT
If the pod deployment fails after the helm install command, go to this page and re-install the reef dependencies (they are probably changed).
https://rndwww.nce.amadeus.net/confluence/pages/viewpage.action?pageId=2112760872

If you have issues with jTracer image pulling:
`docker pull dockerhub.rnd.amadeus.net/docker-production-reef-nce/reef/reef-logstash-jtracer:1.1.0`
`docker tag dockerhub.rnd.amadeus.net/docker-production-reef-nce/reef/reef-logstash-jtracer:1.1.0 reef/reef-logstash-jtracer:1.1.0`

`docker pull dockerhub.rnd.amadeus.net/docker-production-reef-nce/reef-fluentbit-prom-exporter:1.1.0`
`docker tag dockerhub.rnd.amadeus.net/docker-production-reef-nce/reef-fluentbit-prom-exporter:1.1.0 reef-fluentbit-prom-exporter:1.1.0`