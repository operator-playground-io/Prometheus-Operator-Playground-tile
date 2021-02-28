---
title: Monitoring using Prometheus
description: This tutorial explains how Prometheus monitors targets/endpoints
---

### Prometheus Monitoring


Prometheus is designed to monitor the targets including servers, databases, standalone virtual machines, etc.

**Features of Prometheus**
-	Prometheus is a pull-based monitoring system.
-	It actively screens the targets to fetch the corresponding metrics.
-	It periodically scraps the target systems while monitoring.
-	It usually retrieves the metrics via HTTP calls to the endpoints defined in the Prometheus configuration file.

Let’s take the example of MariaDB server and see how Prometheus performs the monitoring.  

### Monitoring MariaDB server using Prometheus 

To monitor the MariaDB server using Prometheus, you need to first install the MariaDB operator and MariaDB server instance by following Step 1 and Step 2 as below. 

You can ignore these steps if you already have MariaDB operator installed and have created the MariaDB Server instance.

**Step 1: Install the MariaDB operator by running the following command.**

```execute
kubectl create -f https://operatorhub.io/install/mariadb-operator-app.yaml             
```

 - **After installation, use the following command to verify that your operator has been successfully installed.**


```execute
kubectl get csv -n my-mariadb-operator-app
```
Please wait until the PHASE status is "Succeeded", then proceed.

You will see a similar output as below:

```
NAME                      DISPLAY            VERSION   REPLACES                  PHASE
mariadb-operator.v0.0.4   Mariadb Operator   0.0.4     mariadb-operator.v0.0.3   Succeeded
```

Note: Once operator is successfully installed, Output PHASE should be as "Succeeded".



 - **Check the Pod status using the command below.**


```execute
kubectl get pods -n my-mariadb-operator-app
```

You will see a similar output as below:

```
NAME                               READY   STATUS    RESTARTS   AGE
mariadb-operator-f96ddc69f-d5vgr   1/1     Running   0          100s
```

Note: In above output, we see the STATUS is "Running" which means that the pods are up and running.


**Step 2: Create MariaDB Server instance and a ` test-db` database along with user details.**

 
  - **Create the below yaml definition of the Custom Resource.**  

```execute
cat <<'EOF' > MariaDBserver.yaml
apiVersion: mariadb.persistentsys/v1alpha1
kind: MariaDB
metadata:
  name: mariadb
spec:
  # Keep this parameter value unchanged.
  size: 1
  
  # Root user password
  rootpwd: password

  # New Database name
  database: test-db
  # Database additional user details (base64 encoded)
  username: db-user 
  password: db-user 

  # Image name with version
  image: "mariadb/server:10.3"

  # Database storage Path
  dataStoragePath: "/mnt/data" 

  # Database storage Size (Ex. 1Gi, 100Mi)
  dataStorageSize: "1Gi"

  # Port number exposed for Database service
  port: 30685
EOF
```

 - **Execute below command to create an instance of MariaDBserver using the above yaml definition:**

```execute
kubectl create -f MariaDBserver.yaml -n my-mariadb-operator-app 
```

Output:

```
mariadb.mariadb.persistentsys/mariadb created
```

 - **Check the Pods status.**

```execute
kubectl get pods -n my-mariadb-operator-app
```

- **Wait until the STATUS of Pods is "Running".**

You will see a similar output as below:

```
NAME                               READY   STATUS    RESTARTS   AGE
mariadb-operator-f96ddc69f-l44r4   1/1     Running   0          10m
mariadb-server-5dccfb7b59-rwzqp    1/1     Running   0          10m
```


**Step 3 : Access MariaDB Database.** 

- Connect to the MariaDB Server Pod.

 - Copy the command below to the terminal, and add the podname of MariaDB Server Instance.
    
```copycommand
 kubectl exec -it <podname> bash -n my-mariadb-operator-app
 ```


 - Connect to the database using username **db-user** and password **db-user**


 ```execute
 mysql -h ##DNS.ip## -P 30685 -u db-user -pdb-user
 ```


- List the database

```execute
show databases;
```


- Exit the database.


```execute
exit
```


- Use the command below to login as root user.


```execute
mysql -h ##DNS.ip## -P 30685 -u root -ppassword
```

- Create the ‘testdb’ database.

```execute
create database testdb;
```


- Use the testdb to create a table 

```execute
use testdb;
```


- Create a table 

```execute
create table Population(year numeric,population numeric);
```

- Insert the data values into the table so you can check MariaDB mySQL metrics from Grafana dashboard.

```execute
insert into Population values(2017,1380004385 );
```

```execute
insert into Population values(2018,1366417754 );
```

```execute
insert into Population values(2019,1352642280 );
```

```execute
insert into Population values(2020,1338676785 );
```


- Retrieve the data from table

```execute
select * from Population;
```

- Exit from testdb :

```execute
exit
```

- Exit from Pod :

```execute
exit
```


**Step 4: Enable monitoring service for MariaDB Server.**


 - **Find the services of MariaDB using below command.**
  
  ```execute
  kubectl get svc -n my-mariadb-operator-app
  ```

 You will see a similar output as below:
 
 ```
      NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
 mariadb-operator-metrics   ClusterIP   10.111.158.91    <none>        8383/TCP,8686/TCP   3d4h
 mariadb-service            NodePort    10.106.178.202   <none>        80:30685/TCP        3d4h
 ```
 
- **Get the port of mariadb-service.**
  
  From above command output, mariadb-service port is `30685`. 

- **Create the below yaml definition of the Custom Resource to enable monitoring using Prometheus exporter pod and service.**

```execute
cat <<'EOF'> MariaDBmonitoring.yaml
apiVersion: mariadb.persistentsys/v1alpha1
kind: Monitor
metadata:
  name: mariadb-monitor
spec:
  # Add fields here
  size: 1
  # Database source to connect with for colleting metrics
  # Format: "<db-user>:<db-password>@(<dbhost>:<dbport>)/<dbname>">
  # Make approprite changes 
  dataSourceName: "root:password@(##DNS.ip##:30685)/test-db"
  # Image name with version
  # Refer https://registry.hub.docker.com/r/prom/mysqld-exporter for more details
  image: "prom/mysqld-exporter"
EOFExecute below command to create an instance of MariaDBserver using the above yaml definition
```

Note: The database host and port should be correct for metrics to work. Host will be IP of the Cluster and the port will be mariadb-service port(see Step 4).


- **Execute below command to create an instance of Monitoring using the above yaml definition.** 

```execute
kubectl create -f MariaDBmonitoring.yaml -n my-mariadb-operator-app
```

Output:

```
monitor.mariadb.persistentsys/mariadb-monitor created
```

This will start Prometheus exporter pod and service. 



- **Check the Pods status.**


```execute
kubectl get pods -n my-mariadb-operator-app
```

You will see a similar output as below:

```
NAME                                          READY   STATUS    RESTARTS   AGE
mariadb-monitor-deployment-7dd85cfbbd-kbbjb   1/1     Running   0          9s
mariadb-operator-f96ddc69f-l44r4              1/1     Running   0          16m
mariadb-server-5dccfb7b59-rwzqp               1/1     Running   0          16m
```

**Step 5: Install Prometheus Operator and Create Instance of Prometheus Server**
 
  - **If you have already installed Prometheus Operator and have created Prometheus Instance and Service, you can skip this Step(Step 5).If not,proceed with below step.**

  - **Use the `Install` button of Prometheus Tutorial to install the Prometheus Operator and `Prometheus Instance Creation` tutorial to create Prometheus Instance and   service.**
  

**Step 6: Create below yaml definition of the Custom Resource to create Instance of ServiceMonitor.**


```execute
cat <<'EOF' > ServiceMonitor.yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: mariadb-monitor 
  labels:
    app: playground
  namespace: operators
spec:
  namespaceSelector:
    any: true
  selector:
    matchLabels:
      tier: monitor-app 
  endpoints:
   - interval: 20s
     port: monitor    
EOF
```


- Execute below command to create an instance of ServiceMonitor.


```execute
kubectl create -f ServiceMonitor.yaml -n operators
```

Output:

```
servicemonitor.monitoring.coreos.com/mariadb-monitor created
```

**Step 6 : Access the Prometheus dashboard using the below link.**


http://##DNS.ip##:30100

On the prometheus UI, 
- Go to Status->Targets to see the endpoints


 ![](_images/targets.PNG)



- Select the query from the dropdown list, and click on “Execute” to check MariaDB metrics. (See the sample snapshot below)


![](_images/queryexecution.PNG)





