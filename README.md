# kube-mediabox
mediabox services for kubernetes via helm

Services:

plex  
sabnzbd  
radarr  
sonarr  
ombi  

For the config pv/pvc of each service it assumes you have a NFS server running on 10.0.1.8.
Either change to the IP to the NFS server youre running or change it to a non NFS pv/pvc.
The config directors on the NFS server are supposed to be in /mnt/config/{servicename}

There is another pv/pvc for shared mount volume for sabnzbd/sonarr/radarr
On my first try I did the same here with a running NFS server but importing of large files took forever, thats why I use a nodeSelector so those 3 service run on the same node.
The nodeSelector label is called mediabox. 
This PV/PVC is a hostPath mount on the node on /mnt/
Please label your node accordingly:
``` kubectl label node3 app=mediabox ```

In my case plex and services have all the media mounted into /mnt/unionfs/Media/ and from there it goes to TV and Movies.
Plex has that folder mounted.
Service that folder via NFS or change it accordingly if you want it on all worker nodes.

First install the media-pv-pvc:

``` helm install media-pv-pvc ./media-pv-pvc ```


Then install the services:

``` helm install sabnzbd ./sabnzbd ```  
``` helm install radarr ./radarr ```  
``` helm install sonarr ./sonarr ```  
``` helm install plex ./plex ```  
``` helm install ombi ./ombi ```

please make sure that UID and GID of folders that are used are set to '911'

TLS is activated, so this assumes you have letsencrypt running on your cluster.

If you havent, delete the following on each ingress, e.g. service.yaml in /radarr:
```
  tls:
  - hosts:
    - radarr.jplex.xyz
    secretName: radarr-tls
```