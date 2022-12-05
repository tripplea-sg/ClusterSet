# InnoDB ClusterSet with Read Replica
This is small tutorial for InnoDB ClusterSet with Read Replica deployment
## Check host files on all MySQL servers
Ensure /etc/hosts file includes all servers
```
10.0.0.166 	clustera-01
10.0.0.57	clustera-02
10.0.0.5	clustera-03
10.0.0.68	clusterb-01
10.0.0.161	clusterb-02
10.0.0.219	clusterb-03
10.0.0.48	readreplica-01
```
## Set my.cnf
Set different server_id on each server 
```
[mysqld]
datadir=/var/lib/mysql
binlog-format=ROW
log-bin=/var/lib/mysql/bin
port=3306
server_id=1
socket=/var/lib/mysql/mysqld.sock
log-error=/var/log/mysqld.log
enforce_gtid_consistency = ON
gtid_mode = ON
log_slave_updates = ON
```
Start mysql using
```
sudo systemctl start mysqld
```
Check /var/log/mysqld.log for temporary root password </br>
Login to MySQL and change root password to Root#123. 
## Configure Instance
Run configure-instance against all instances
```
mysqlsh -- dba configure-instance { --host=127.0.0.1 --port=3306 --user=root --instance-password=Root#123 } --clusterAdmin=gradmin --clusterAdminPassword='Grpass#123' --interactive=false --restart=true
```
## Create InnoDB ClusterSet
Login into 1st server and create InnoDB Cluster
```
mysqlsh -ugradmin -p"Grpass#123" -h::1 -- dba createCluster mycluster --consistency=BEFORE_ON_PRIMARY_FAILOVER
mysqlsh gradmin@localhost:3306 -- cluster add-instance gradmin@clustera-02:3306 --recoveryMethod=clone
mysqlsh gradmin@localhost:3306 -- cluster add-instance gradmin@clustera-03:3306 --recoveryMethod=clone

mysqlsh gradmin@localhost:3306 -- cluster create-cluster-set clusterset
```
## Add 2nd cluster
```
mysqlsh gradmin@localhost:3306 -- clusterset createReplicaCluster gradmin@clusterb-01:3306 cluster2 --recoveryMethod=clone
```
Connect to PRIMARY node of 2nd cluster and add 2nd & 3rd nodes to Replica Cluster
```
mysqlsh gradmin@localhost:3306 -- cluster add-instance gradmin@clusterb-02:3306 --recoveryMethod=clone
mysqlsh gradmin@localhost:3306 -- cluster add-instance gradmin@clusterb-03:3306 --recoveryMethod=clone
```
## Add Read Replica
Connect to Read Replica
```

```
