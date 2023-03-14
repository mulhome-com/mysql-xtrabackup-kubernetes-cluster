# mysql-xtrabackup-kubernetes-cluster
Build the mysql cluster in the kubernetes including the xtrabackup which to backup data asynchronously

Requirement Environment:

- Kubernetes
- NFS
- Sysbench


Process:


> kubectl -n ${namespace} apply -f mysql-configmap.yaml

- Prepare the replica.cnf used in the slave server and primary.cnf used in the master server in the config map.


> kubectl -n ${namespace} apply -f mysql-secret.yaml

- Store the user and password for mysql connection


> kubectl -n ${namespace} apply -f mysql-service.yaml

- Build the read-only service which use nodeport to performance testing for statefulset


> kubectl -n ${namespace} apply -f mysql-statefulset.yaml

- Setup the statefulset to create referenced pod entity


> kubectl -n ${namespace} apply -f mysql-service-master.yaml

- Build the master service which can be write.


> kubectl -n ${namespace} get pod --watch

- Check the status




Test:


> kubectl -n ${namespace} get svc

- List the service for node port


read-write-service: 30769
read-only-service: 30306 
pod-server: testsvr



- Run the sysbench test

> sysbench /usr/share/sysbench/oltp_read_only.lua --threads=48 --tables=50 --db-driver=mysql --mysql-host=testsvr --mysql-user=admin --mysql-password=Esonew2Z --mysql-port=30768 prepare

> sysbench /usr/share/sysbench/oltp_read_only.lua --threads=48 --tables=40 --db-driver=mysql --mysql-host=testsvr --mysql-user=admin --mysql-password=Esonew2Z --mysql-port=30306 run


Get the result

>    queries:                             169392 (16270.64 per sec.)


