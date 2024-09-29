# Overview

An Emerald Global Edge Setup consists of the following main building blocks:  
The NCR Edge Management Console.  
The NCR EDGE GCP Foreman Project.  
An Edge Store Cluster - server (controlplance) and touchpoints (worker nodes).  
An Emerald Global Managed HQ Services instance.  
Emerald Global Charts deployed onto the Edge store cluster.  

# I don't know what Edge is - where do I start?

The [edge wiki](https://docs.edge-infra.dev/edge/) would be a good place to start

# I don't know what Emerald Global is - where do I start?

[Emerald Global Training Sessions](https://confluence.ncr.com/display/RED/Emerald+Global+Training+Sessions)



# Who to Contact When?

Managed Emerald Global HQ instances - contact the Emerald Global DevOps Team  
Edge Onboarding and Support - the NCR Edge team  
Client questions and issues (XLR, Devices) - The Emerald Global XLR Team   
Server questions and issues - the Emerald Global Server Team  
Helm chart and deployment questions and issues - the Emerald Global DevOps Team  

# Focal Points and Communication Channels

The Emerald Global DevOps Team - the [#emeraldglobal-edge-support](https://ncr.enterprise.slack.com/archives/C05EVLHA8KE) slack channel  
The Emerald Global XLR Team - [Ronen Zeevi](https://confluence.ncr.com/display/~rz185035)   
The Emerald Global Server Team -  [Tomer Guri](https://confluence.ncr.com/display/~tg250098)  
Edge Support - onboarding questions can be raised in the [#ncr-edge-onboarding](https://ncr.enterprise.slack.com/archives/C01M0008P0E) and issues in the [#ncr-edge-incidents](https://ncr.enterprise.slack.com/archives/C020D3G8S2J) slack channel. For joining these (private) slack channels,  
please reach out to [Venkat Murali](https://confluence.ncr.com/display/~vm250200)  



# Perquisites  
1. Need to allow edge connection by providing the `Control Plan` IP.  
   a. login with ssh to `Control Plan` of edge store.  
   b. run ``` curl https://ipinfo.io/ip ```  
   c. copy the IP and send it in  
    [#emeraldglobal-edge-support](https://ncr.enterprise.slack.com/archives/C05EVLHA8KE) slack channel 
2. In EDGE portal create `secret` for `emeraldgx` deployment.  


# Create new `Workload`

1. In edge portal , enter your store name , e.g `gx-dev1`  
2. Under `Workloads` tab , click on `new workload`. 

## STORE 

### App details screen

1. Repository: `ncr-charts`. 
2. Helm Chart: `emerald-gx`  
3. Chart Version: e.g `24.2.0`
4. App Name: `gx-store`. 
5. Namespace: `gx-store`. 
6. Config Map: `Edge config map`


### Secrets screen

1. Select your STORE secret, e.g `emerald-demo-us-store-secrets`

### Node target screen

1. Select `Server`

### Date and time screen

1. `Deploy immediately`

### default values.yaml

### Modify Values:
```
serverType: store - When deploy STORE.  
secretName: <STORE_SECRET>.  
hqServerName: <HQ_APP_ENDPOINT> # [Standard] hq-demo-us.test.ncrsaas.com OR [Multi Tenants] emeraldgx-us.test.ncrsaas.com/server/demo.  
dmsParentUrl: <HQ_DMS_ENDPOINT> # [Standard] https://dms-demo-us.test.ncrsaas.com OR [Multi Tenants] https://emeraldgx-us.test.ncrsaas.com/dms/demo.  
rabbitParentServerName: <HQ_RABBITMQ_ENDPOINT> # rabbit-us.emeraldgx.test.ncrsaas.com.  
rabbitParentVirtualHost: "<RABBITMQ_VIRTUAL_HOST>" # "ncrdemo" OR "/".  
url: <HQ_NOTIFICATION_ENDPOINT> # [Standard] https://notification-demo-us.test.ncrsaas.com OR [Multi Tenants] https://emeraldgx-us.test.ncrsaas.com/notification/demo.  
```
```

global:
  serverType: store
  namespace: gx-store 
  secretName: <STORE_SECRET>
  imagePullSecrets:
  - name: edge-docker-pull-secret  
  # nodeSelectors
  # Used for targeting specific nodes based on their kubernetes labels nodeSelector: "node.ncr.com/sub-class: xlr"
  nodeSelector: null

emerald-gx:
  hqServerName: <HQ_APP_ENDPOINT>
  dmsParentUrl: <HQ_DMS_ENDPOINT>
  rabbitParentServerName: <HQ_RABBITMQ_ENDPOINT>
  rabbitParentVirtualHost: "<RABBITMQ_VIRTUAL_HOST>"
  parentUrl: null
# Multi Rabbit Enable section for Store level only
  multiRabbitParentUpload:
    enabled: false
    rabbitParentServerName: null
    rabbitParentVirtualHost: null
  multiRabbitExternalRabbitExporter:
    enabled: false
    rabbitParentServerName: null
    rabbitParentVirtualHost: null
      
  selling:
    modules:
      enabledExternalModules: ""
    logging:
      level: Error
  
  datasync:
    logging:
      level: Error
    notification:
      enabled: false
      url: <HQ_NOTIFICATION_ENDPOINT>

  emissaryIngress:
    enabled: false

```  






## POS 

1. In edge portal , enter your store name , e.g `gx-dev1`  
2. Under `Workloads` tab , click on `new workload`. 

### App details screen

1. Repository: `ncr-charts`. 
2. Helm Chart: `emerald-gx`  
3. Chart Version: e.g `24.2.0`
4. App Name: `gx-pos`. 
5. Namespace: `gx-pos`. 
6. Config Map: `Edge config map`


### Secrets screen

1. Select your POS secret, e.g `emerald-global-pos-secrets`

### Node target screen

1. Select `Touchpoint`

### Date and time screen

1. `Deploy immediately`

### default values.yaml
### Modify Values:
```
serverType: pos - When deploy POS.  
secretName: <POS_SECRET> # e.g emerald-global-pos-secrets
hqServerName: <HQ_APP_ENDPOINT> # [Standard] hq-demo-us.test.ncrsaas.com OR [Multi Tenants] emeraldgx-us.test.ncrsaas.com/server/demo.  
url: <HQ_NOTIFICATION_ENDPOINT> # [Standard] https://notification-demo-us.test.ncrsaas.com OR [Multi Tenants] https://emeraldgx-us.test.ncrsaas.com/notification/demo.  
```
```

global:
  serverType: pos
  namespace: gx-pos 
  secretName: <POS_SECRET> 
  imagePullSecrets:
  - name: edge-docker-pull-secret  
  # nodeSelectors
  # Used for targeting specific nodes based on their kubernetes labels nodeSelector: "node.ncr.com/sub-class: xlr"
  nodeSelector: null

emerald-gx:
  
  hqServerName: <HQ_APP_ENDPOINT>
  dmsParentUrl: "http://gx-datasync.gx-store.svc.cluster.local:8085"
  rabbitParentServerName: rabbitmq.gx-store.svc.cluster.local
  rabbitParentVirtualHost: "/"
  parentUrl: http://gx-selling.gx-store.svc.cluster.local/storewebservices
# Multi Rabbit Enable section for Store level only
  multiRabbitParentUpload:
    enabled: false
    rabbitParentServerName: null
    rabbitParentVirtualHost: null
  multiRabbitExternalRabbitExporter:
    enabled: false
    rabbitParentServerName: null
    rabbitParentVirtualHost: null
      
  selling:
    modules:
      enabledExternalModules: ""
    logging:
      level: Error
  
  datasync:
    logging:
      level: Error
    notification:
      enabled: true
      url: <HQ_NOTIFICATION_ENDPOINT>

  emissaryIngress:
    enabled: false


```  

## retail-emerald-desktop

1. In edge portal , enter your store name , e.g `gx-dev1`  
2. Under `Workloads` tab , click on `new workload`. 

### App details screen

1. Repository: `ncr-charts`. 
2. Helm Chart: `emerald-gx-xlr`  
3. Chart Version: e.g `24.2.0`
4. App Name: `retail-emerald-desktop`. 
5. Namespace: `retail-emerald-desktop`. 
6. Config Map: `Not Need`


### Secrets screen

1. `Not Need`

### Node target screen

1. Select `Touchpoint`

### Date and time screen

1. `Deploy immediately`


### default values.yaml
```
global:
  namespace: retail-emerald-desktop
  # example for nodeSelector for xlr
  # nodeSelector: "node.ncr.com/hostname: s1-worker-1"
emerald-desktop-bff:
  variables:
    rtiUrl: http://gx-selling.gx-pos.svc.cluster.local/storewebservices

```

## emerald-touchpoint

1. In edge portal , enter your store name , e.g `gx-dev1`  
2. Under `Workloads` tab , click on `new workload`. 

### App details screen

1. Repository: `ncr-charts`. 
2. Helm Chart: `emerald-gx-xlr-touchpoint`  
3. Chart Version: e.g `24.2.0`
4. App Name: `emerald-touchpoint`. 
5. Namespace: `emerald-touchpoint`. 
6. Config Map: `Not Need`

### Secrets screen

1. `Not Need`

### Node target screen

1. Select `Touchpoint`

### Date and time screen

1. `Deploy immediately`

### default values.yaml
```
global:
  namespace: emerald-touchpoint
  # used for targeting only some lanes, example for nodeSelector for xlr
  # nodeSelector: "node.ncr.com/hostname: s1-worker-1"
xlr-electron-ui:
  variables:
    electronThinAppUrl: "http://retail-desktop-pos.retail-emerald-desktop/"
    apiHost: "http://emerald-desktop-bff.retail-emerald-desktop"
    tz: "UTC"
emerald-xlr-rps:
  variables:
    epsPort: 13000
    epsServer: null
```



## coldstart

1. In edge portal , enter your store name , e.g `gx-dev1`  
2. Under `Workloads` tab , click on `new workload`. 

### App details screen

1. Repository: `ncr-charts`. 
2. Helm Chart: `emerald-gx-utils`  
3. Chart Version: e.g `1.0.0-beta.1`
4. App Name: `cs-<STORE_OR_POS>`. 
5. Namespace: `gx-<STORE_OR_POS>`. 
6. Config Map: `Not Need`

### Secrets screen

1. `Not Need`

### Node target screen

1. Select `No modification`

### Date and time screen

1. `Deploy immediately`


### default values.yaml
```

# This chart is used for initializing a GX store.
# To initialize a GX store, first cold-start the GX store server,
# and then cold-start the GX touchpoints.
utils:
  
  # The two available tasks are "coldstart" and "snapshot"
  # A GX store workload must be successfully cold-started to be considered operational.
  # After the GX 
  task: "coldstart"
  
  # runId can be used for re-triggering an already deployed workload.
  # The runId will be embedded in the kubernetes job and pod name, ensuring a new job kicks in without needing to redeploy the workload.
  runId: null

  # Use node selector if you don't want to use edge labels.
  # When using node selector, select the "No modification" option in the Node targeting dialog.
  nodeSelector: 
    node.ncr.com/hostname: <HOSTNAME>
```



# View logs 

1. In edge portal , enter your store name , e.g `gx-dev1`  
2. From the top right screen select `View cluster logs`. 

In GCP `Logs Explorer` you can filter for emeraldgx application.  

e.g - logs from all STORE namespace
```
labels.cluster_edge_id=<YOUR_CLUSTER_EDGE_ID>
resource.labels.namespace_name="gx-store"

```

e.g - logs only from STORE selling pod
```
labels.cluster_edge_id=<YOUR_CLUSTER_EDGE_ID>
resource.labels.namespace_name="gx-store"
resource.labels.container_name="gx-selling"

```

e.g - logs only from POS datasync pod
```
labels.cluster_edge_id=<YOUR_CLUSTER_EDGE_ID>
resource.labels.namespace_name="gx-pos"
resource.labels.container_name="gx-datasync"

```

# Common commands after `emeraldgx` deployment

e.g - ssh login to `Control Plan` edge store.  

```

gcloud compute ssh zylevel0@s4-master-1 --project=ret-edge-dsds-r10 --zone=europe-west2-c
```

e.g - get all gx-store workloads


```
kubectl -n gx-store get pod -o wide
```

e.g - get all gx-pos workloads


```
kubectl -n gx-pos get pod -o wide
```

e.g - get logs from gx-store


```
kubectl -n gx-store logs deploy/gx-selling -c gx-selling

kubectl -n gx-store logs deploy/gx-datasync -c gx-datasync
```

e.g - get logs from gx-pos


```
kubectl -n gx-pos logs deploy/gx-selling -c gx-selling

kubectl -n gx-pos logs deploy/gx-datasync -c gx-datasync
```

e.g - run coldstart on gx-store


```
kubectl -n gx-store exec deploy/gx-utils -c utils -- utils coldstart
```

e.g - run coldstart on gx-pos

```
kubectl -n gx-pos get po -o wide
gx-utils-sxp7r      2/2     Running   0 100.127.88.54     s4-worker-1 = TP 1
gx-utils-fvd57      2/2     Running   0 100.127.186.232   s4-worker-2 = TP 2
etc..
kubectl -n gx-pos exec pod/gx-utils-<POD_HASH> -c utils -- utils coldstart
```

e.g - invoke with sqlserver


```
GX-STORE  
kubectl -n gx-store exec sqlserver-0 -c ms-sqlserver -it -- /bin/bash  
 
GX-POS  
kubectl -n gx-pos exec sqlserver-1-0 -c ms-sqlserver -it -- /bin/bash
kubectl -n gx-pos exec sqlserver-2-0 -c ms-sqlserver -it -- /bin/bash
kubectl -n gx-pos exec sqlserver-3-0 -c ms-sqlserver -it -- /bin/bash
etc...  


opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P Qwe12345 -d retail -Q "select * from ConfigurationEntry" -Y 30


opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P Qwe12345 -d dms -Q "select * from DMS_ContextData" -Y 30

opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P Qwe12345 -d dms -Q "select * from DMS_PhysicalToLogical" -Y 30

opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P Qwe12345 -d dms -Q "select * from DMS_ServersHierarchy" -Y 30

opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P Qwe12345 -d dms -Q "select * from DMS_ServerMonitoringInfo" -Y 30
```
