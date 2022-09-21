# APIC-v10-Analytics-running-out-of-disk-space-vmware-resoluton  
If the analytics have stopped logging due to the system disk being full, here are instructions to release space.  
NOTE: Please always to double check whether new instructions are updated on the [IBM documention site](https://www.ibm.com/docs/en/api-connect) in addition to these instructions for enhanced handling of issues.
  
**!!WARNING!! This will purge your analytics data. If you need to backup your analytics, please take a look at the "[Backing up and restoring the Analytics database on VMware](https://www.ibm.com/docs/en/api-connect/10.0.1.x?topic=later-backing-up-restoring-analytics-database-vmware)" documentation on IBM help, or contact IBM support.**  
*****  
### Steps to clear space off each VM node  

The follow steps assume that you are logged into the Analytics subsystem.
You will be applying the steps to each node of the subsystem (e.g. if in a cluster, at least 3 VMs, if stand-alone, just 1 VM).  
  
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

This will have to be completed for all the analytic subsystems in the cluster.

*****  
### Post-Resolution validation  
1. 






