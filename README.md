# kube-mediabox
mediabox services for kubernetes via helm

This is still very basic and a WIP. But its working for now.

Services:

plex  
sabnzbd  
radarr  
sonarr  
ombi  

For the config of each service the cluster needs it on a cluster with a nodeSelector mediabox. It is supposed to be in /config/{servicename}

There is a pv/pvc for shared mount volume for sabnzbd/sonarr/radarr
On my first try I did the same here with a running NFS server but importing of large files took forever, thats why I use a nodeSelector so those 3 service run on the same node.
The nodeSelector label is called mediabox. 
This PV/PVC is a hostPath mount on the node on /mnt/
Please label your node accordingly:
``` kubectl label node3 app=mediabox ```

In my case plex and services have all the media mounted into /mnt/unionfs/Media/ and from there it goes to TV and Movies.
Plex has that folder mounted.
Service that folder via NFS or change it accordingly if you want it on all worker nodes.

Please change hostname of each service under service/templates/ingress.yaml
you will find e.g.````radarr.yourservice.xyz````
Change it to your hostname, e.g. ```radarr.mediabox.xyz```
TLS is activated, so this assumes you have letsencrypt running on your cluster.
If you havent, delete the following on each ingress, e.g. service.yaml in /radarr:
```
  tls:
  - hosts:
    - radarr.yourservice.xyz
    secretName: radarr-tls
```

First install the media-pv-pvc:

``` helm install media-pv-pvc ./media-pv-pvc ```


Then install the services:

``` helm install sabnzbd ./sabnzbd ```  
``` helm install radarr ./radarr ```  
``` helm install sonarr ./sonarr ```  
``` helm install plex ./plex ```  
``` helm install ombi ./ombi ```

please make sure that UID and GID of folders that are used are set to '911'


If you run into permission issues try to chown the folders first before installing via helm.