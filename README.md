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
Check /var/log/mysqld.log for temporary root password <\br>
Login to MySQL and change root password to Root#123. 
