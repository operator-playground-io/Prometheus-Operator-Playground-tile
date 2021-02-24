---
title: Prometheus Operator cleanup. 
description: Learn how to cleanup Prometheus Operator.
---


### Cleaning Up Operator



**Step 1: Delete the operator's Custom Resources by using `kubectl delete` commands as below.**

 
 ```execute
 kubectl delete -f prometheusInstance.yaml -n operators
 kubectl delete -f prometheus_service.yaml -n operators
 kubectl delete -f MariaDBserver.yaml -n my-mariadb-operator-app
 kubectl delete -f MariaDBmonitoring.yaml -n my-mariadb-operator-app
 kubectl delete -f ServiceMonitor.yaml -n operators 
 ```


**Step 2: Delete the operator by using `kubectl delete` command as below.**
 
 
 Example:
 
 ```execute
 kubectl delete -f https://operatorhub.io/install/prometheus.yaml
 kubectl delete -f https://operatorhub.io/install/mariadb-operator-app.yaml
 ```
 

**Step 3: Deleting the CSV resource. ***

 **Step 3.1: Find the Prometheus CSV in the namespace "Operators".**


```
kubectl get csv -n operators
```

Output:
```
NAME                        DISPLAY               VERSION   REPLACES                    PHASE
prometheusoperator.0.37.0   Prometheus Operator   0.37.0    prometheusoperator.0.32.0   Succeeded
```

 **Step 3.1: Delete the CSV.**


```
kubectl delete csv/prometheusoperator.0.37.0 -n operators
```

Note: The csv value may be different from above value.In the above delete csv command,Use the csv retrived by kubectl get csv command.  
 

**Step 4: Delete all the yaml files.**

 
  ```execute
  rm -rf prometheusInstance.yaml 
  rm -rf prometheus_service.yaml
  rm -rf MariaDBserver.yaml
  rm -rf MariaDBmonitoring.yaml
  rm -rf ServiceMonitor.yaml
 ```
  
### Conclusion

You have successfully cleaned up the Prometheus Operator resources.
