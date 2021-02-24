---
title: Prometheus Instance Creation 
description: Learn how to create instances of your Prometheus Operator
---

###  Create Prometheus Instance.

**Step 1: First, create the yaml definition of the Custom Resource for Prometheus Instance as below.** 

```execute
cat <<'EOF' > prometheusInstance.yaml
apiVersion: monitoring.coreos.com/v1
kind: Prometheus
metadata:
  name: server
  labels:
    prometheus: k8s
  namespace: operators
spec:
  replicas: 1
  serviceAccountName: prometheus-k8s
  securityContext: {}
  serviceMonitorSelector:
    matchLabels:
      app: playground  
  alerting:
    alertmanagers:
      - namespace: operators
        name: alertmanager-main
        port: web  
EOF
```

**Step 2: Execute the command below to create your Prometheus instance .** 

```execute
kubectl create -f prometheusInstance.yaml -n operators
```

You will see the following resources created:

```output
prometheus.monitoring.coreos.com/prometheus created
```

**Step 3: Wait till Pod STATUS is "Running", then proceed.**


**Step 4: Check the pod status.**

```execute
kubectl get pods -n operators
```
You will see a similar output as below:

```output
NAME                                   READY   STATUS    RESTARTS   AGE
prometheus-operator-6f7589ff7f-qdh9c   1/1     Running   0          102s
prometheus-server-0                    3/3     Running   1          26s
```

Wait till Pod STATUS is "Running", then proceed.

### Create Prometheus Service of type NodePort 

**Step 1: First, create the yaml definition as below.


```execute
cat <<'EOF' > prometheus_service.yaml
apiVersion: v1
kind: Service
metadata:
  name: prometheus
spec:
  type: NodePort
  ports:
  - name: web
    nodePort: 30100
    port: 9090
    protocol: TCP
    targetPort: web
  selector:
    app: prometheus
EOF
```

**Step 2: Execute the command below to create your Prometheus Service.** 

```execute
kubectl create -f prometheus_service.yaml -n operators
```

Output:

```
service/prometheus created
```

**Step 3: Find the port for `NodePort` service using the following command.**

```execute
kubectl get svc -n operators
```

we can see the output NodePort is `30100`

**Step 4: Click on below link to access your Prometheus dashboard.** 

http://##DNS.ip##:30100

You will see the Prometheus dashboard as below :

![prometheus-page](_images/prom1.png)


