---
title: Prometheus Operator cleanup Tutorial
description: This tutorial explains how to cleanup Operator
---


### Cleaning Up Operator




***Delete the operator's Custom Resources  by kubectl delete commands :***

 

Example:
 
 ```
 kubectl delete -f prometheusInstance.yaml -n operators
 ```

Note: Here prometheusInstance.yaml is the Custom Resource  of the Prometheus Server Instance.
Similarly,delete all the Custom Resource s.
 

***Delete the operator by kubectl delete command:***
 
 
 Example:
 
 ```
 kubectl delete -f https://operatorhub.io/install/prometheus.yaml
 ```
 

***Deleting the CSV resource ***

- Find the CSV in the namespace

Example:

```
kubectl get csv -n operators
```

- Delete that CSV :

Example:

```
kubectl delete csv/prometheusoperator.0.37.0 -n operators
```

 
***Delete all the yaml files:***
 
 Example:
 
  ```
  rm -rf prometheusInstance.yaml 
  ```
  

