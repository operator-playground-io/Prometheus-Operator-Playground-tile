---
title: Prometheus Operator cleanup Tutorial
description: This tutorial explains how to cleanup Operator
---


### Cleaning Up Operator




***Delete the operator's Custom Resources  by kubectl delete commands :***

 
 ```execute
 kubectl delete -f prometheusInstance.yaml -n operators
 kubectl delete -f prometheus_service.yaml -n operators
 kubectl delete -f MariaDBserver.yaml -n my-mariadb-operator-app
 kubectl delete -f MariaDBmonitoring.yaml -n my-mariadb-operator-app
 kubectl delete -f ServiceMonitor.yaml -n operators 
 ```


***Delete the operator by kubectl delete command:***
 
 
 Example:
 
 ```execute
 kubectl delete -f https://operatorhub.io/install/prometheus.yaml
 kubectl delete -f https://operatorhub.io/install/mariadb-operator-app.yaml
 ```
 

***Deleting the CSV resource ***

- Find the CSV in the namespace

Example:

```execute
kubectl get csv -n operators
```

Output:
```
NAME                        DISPLAY               VERSION   REPLACES                    PHASE
prometheusoperator.0.37.0   Prometheus Operator   0.37.0    prometheusoperator.0.32.0   Succeeded
```

Delete above CSV value using kubectl delete csv command:

```execute
kubectl delete csv/prometheusoperator.0.37.0 -n operators
```
Note: The csv value may be different from above value.In the above delete csv command,Use the csv retrived by kubectl get csv command.  
 

***Delete all the yaml files:***
 

 
  ```execute
  rm -rf prometheusInstance.yaml 
  rm -rf prometheus_service.yaml
  rm -rf MariaDBserver.yaml
  rm -rf MariaDBmonitoring.yaml
  rm -rf ServiceMonitor.yaml
 ```
  

