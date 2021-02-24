---
title: Prometheus Operator Installation Verification
description: Learn how to verify that the Prometheus Operator has been properly installed in the namespace.
---

### Prometheus Operator status verification 

**Step 1: Verify that the Prometheus Operator has been installed successfully by executing below command.**

```execute
kubectl get csv -n operators
```

You will see a similar output as below.

```output
NAME                        DISPLAY               VERSION   REPLACES                    PHASE
prometheusoperator.0.37.0   Prometheus Operator   0.37.0    prometheusoperator.0.32.0   Succeeded
```

Note: Please wait for the PHASE status to be `Succeeded`, then proceed.

**Step 2: Check the pod status.**

```execute
kubectl get pods -n operators
```

You should see the pod details as:

-	Name starting with 'kong-operator'
-	Ready value as '1/1' 
-	Status as 'Running'.

See the sample output below.

```output
NAME                                   READY   STATUS    RESTARTS   AGE
prometheus-operator-6f7589ff7f-hswnz   1/1     Running   0          6m48s
```

