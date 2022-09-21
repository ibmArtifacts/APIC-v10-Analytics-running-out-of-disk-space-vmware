# APIC-v10-Analytics-running-out-of-disk-space-vmware-resoluton  
If the analytics have stopped logging and disk is filled up, here are instructions to release space.  
  
**!!WARNING!! This will purge your analytics data. If you need to backup your analytics, please take a look at the "[Backing up and restoring the Analytics database on VMware](https://www.ibm.com/docs/en/api-connect/10.0.1.x?topic=later-backing-up-restoring-analytics-database-vmware)" documentation on IBM help, or contact IBM support.**  

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
