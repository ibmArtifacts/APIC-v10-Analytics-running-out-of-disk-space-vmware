# APIC-v10-Analytics-running-out-of-disk-space-vmware-resoluton  
If the analytics have stopped logging due to the system disk being full, here are instructions to release space.  

The error seen from this issue is "_Max Recommended Usage: 70% | Total disk used: ##%_" from a ``apic health-check`` command check on the Analytics subsystem.

NOTE: Please always to double check whether new instructions are updated on the [IBM documention site](https://www.ibm.com/docs/en/api-connect) in addition to these instructions for enhanced handling of issues.
  
**!!WARNING!! This will purge your analytics data. If you need to backup your analytics, please take a look at the "[Backing up and restoring the Analytics database on VMware](https://www.ibm.com/docs/en/api-connect/10.0.1.x?topic=later-backing-up-restoring-analytics-database-vmware)" documentation on IBM help, or contact IBM support.**  
*****  

### Steps to clear space off each VM node  

The follow steps assume that you are logged into the Analytics subsystem.
You will be applying the steps to each node of the subsystem (e.g. if in a cluster, at least 3 VMs, if stand-alone, just 1 VM).  
  
**Remember** to enter sudo mode with ``sudo -i``  
   
1. Find which location all the indices are being logged to:
```
find /data/secure/volumes -name "indices"
```  

2. If output is ``/data/secure/volumes/vol1/nodes/0/indices`` then navigate to the parent directory of the indices as shown below:  
```
cd /data/secure/volumes/vol1/nodes/0
```  

3. Remove the indices folder:
```
rm -rf ./indices/
```  

4. Restart the analytics:  
```
systemctl restart appliance-manager
```  

5. Refresh the analytics storage-shared pod:
```
kubectl get pods

kubectl delete pod {name_of_storage-shared_pod}
```  

6. Reboot the VM:  
```
shutdown -r now
```  
Sample of the full process:  
  
```
root@analytics:~# sudo -i
root@analytics:~# find /data/secure/volumes -name "indices"
/data/secure/volumes/vol9/nodes/0/indices
root@analytics:~# rm -rf /data/secure/volumes/vol9/nodes/0/indices
root@analytics:~# systemctl restart appliance-manager
root@analytics:~# kubectl get pods
NAME                                    READY   STATUS      RESTARTS   AGE
analyt-nj-cj-retention-27728730-sh7g7   0/1     Completed   0          16h
analyt-nj-cj-rollover-27729735-j9955    0/1     Completed   0          32s
analyt-nj-client-996987d88-5f8j8        1/1     Running     1          17d
analyt-nj-client-996987d88-jmmjh        1/1     Running     0          25h
analyt-nj-client-996987d88-rzbqj        1/1     Running     0          25h
analyt-nj-ingestion-d6d48c6df-275kg     1/1     Running     0          25h
analyt-nj-ingestion-d6d48c6df-8jtvh     1/1     Running     0          25h
analyt-nj-ingestion-d6d48c6df-dhpnn     1/1     Running     0          7d23h
analyt-nj-mtls-gw-7894c488fc-8jjsx      1/1     Running     0          25h
analyt-nj-mtls-gw-7894c488fc-q4lj5      1/1     Running     0          25h
analyt-nj-mtls-gw-7894c488fc-wc7rg      1/1     Running     2          154d
analyt-nj-storage-shared-0              1/1     Running     1          7d21h
analyt-nj-storage-shared-1              1/1     Running     3          7d21h
analyt-nj-storage-shared-2              1/1     Running     1          26h
ibm-apiconnect-75b7bc4c6-4f4wd          1/1     Running     2          17d
root@analytics:~# kubectl delete pod analyt-nj-storage-shared-0
pod "analyt-nj-storage-shared-0" deleted
root@analytics:~# shutdown -r now
```


*****  
### Post-Resolution validation  
  
**Remember** to enter sudo mode with ``sudo -i``  
  
1. Once the Analytic subsystem comes back up, issue ``apic status`` to review the "Disk Space Available" freed up.  
2. Check that analytics client, ingestion, and mtls pods are evenly distributed across all nodes of the cluster (if you have a cluster) -- this is not required if you're on stand-alone.  
  ```
  kubectl get nodes
  {a list of nodes will output}
  
  kubectl describe node {node}
  ```  
A sample of a healthy disbused system would only contain 1 client, ingestion, and mtls pod. 
If there are more than 1, please use kubectl delete pod {pod_name} to delete 2 of the pods.  
  
![image](https://user-images.githubusercontent.com/66093865/191584797-e949584b-dceb-4dc8-ae8c-5536f6bf017f.png)  


  






