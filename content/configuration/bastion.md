# Bastion connection

# GCP


First make sure you generate `password` in order to login to vm,
from google portal under gcp project.  

1. Login to gcp project.   
1. Go to `VM instances`.
2. Select vm name e.g `multi-tenant-windows-bastion-support-us-test`.
3. Click on `SET WINDOWS PASSWORD`.
4. Your `quicklookid` will show DO NOT CHANGE "username" login , click `set`. 

Once `password` was generated save it locally for later use.

The Windows Bastion runs under the identity of a dedicated service account.

Windows users download from here:  
```
https://github.com/GoogleCloudPlatform/iap-desktop  
and authenticate with their GCP login.
``` 
Mac users can connect using gcloud compute start-iap-tunnel

See below the gcloud compute start-iap-tunnel for connecting to the windows bastion server.   
```
gcloud compute start-iap-tunnel multi-tenant-windows-bastion-support-us-test 3389 --project ret-emeraldgxsaas-cug01-test
```


# AZURE

1. Login to azure portal.   
2. Go to `Virtual Machines`.
3. Select vm name.
4. Click on `Connect`.
5. Click on `Bastion`.  
