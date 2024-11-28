## Target CS Version
From myamadeus JCP POD
```yaml
platform:
  envOverrideHelm:
    common:
      centralStorage:
        server:
          url: http://secocs-seco-cs-pr-898.i2.nce2.paas.amadeus.net
```
From appstore RSF POD
```yaml
      reef:
        appstore:
          centralStorage:
            server:
              url: https://secocs-seco-cs-pr-1134.app.net6.paas.we...
```

## Target CS from SECO config
Edit a file in SECO config:

`blueprints/src/main/helm/seco-app/templates/seco-app-deployment.yaml`
And add:  (with the correct URL, as in the PR specification)
![[Pasted image 20241127103555.png]]

## Target DB
```yaml
platform:
  dbSpec:
    - reference: dbaas-jcp-master
      action: db-clone
    - reference: dbaas-acm-master
      action: db-clone
  envOverrideHelm:
    common:
      database:
        jcp:
          server: NCEREEFDEVSQL1.rnd.amadeus.net
          name: SECO_CS_PR_887_JCP
        acm:
          server: NCEREEFDEVSQL1.rnd.amadeus.net
          name: SECO_CS_PR_887_ACM
```



SECO BK
```yaml
platform:
  dbSpec:
    - reference: dbaas-jcp-master
      action: db-clone
    - reference: dbaas-acm-master
      action: db-clone
  envOverrideHelm:      
    common:
      rcs:
        sessionRepository: in-memory
      database:
        jcp:
          server: NCEREEFDEVSQL1.rnd.amadeus.net
          name: SECO_BK_PR_8880_JCP
        acm:
          server: NCEREEFDEVSQL1.rnd.amadeus.net
          name: SECO_BK_PR_8880_ACM
```
## Target different ENV artifact
```yaml
platform:
  envSpec:
    pullRequest: 258
```

## OpenShift console debug

Go to the openshift console and choose the namespace (high left)
Then go to -> applications -> Deployments, select your deployments
Then -> action -> edit YAML
In the yaml:
remove the livenessProbe definition from the seco-domain image (or myamadeus-domain)
set the BP_JBOSS_DEV_DEBUG_PORT_BE to true
Now it will load again a deployment
Locally open the `oc` CLI and insert:

|                                                                           |                                |
| ------------------------------------------------------------------------- | ------------------------------ |
| oc login openshift.nce2.paas.amadeus.net:8443 -u USERNAME -p PASSWORD     | AMAISDOM credentials           |
| oc project REMOTE_NAMESPACE                                               | namespace name                 |
| oc get pods                                                               | get the list of pods available |
| oc port-forward REMOTE_SECOAPP_PODNAME LOCAL_DEBUG_PORT:REMOTE_DEBUG_PORT | ports to be used for debug     |

Then in Intellij start the debugger with the remote configuration, make sure that the port is right