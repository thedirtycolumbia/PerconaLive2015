## FAST MASTER FAILOVER WITHOUT DATA LOSS
_Yoshinori Matsunobu (Database Engineer, Facebook)_

Semisync Replication can use InnoDB, is easy to extend, but is not fully sync and has no fast failover mechanism.

Facebook uses loss-less semisync: InnoDB prepare => Binlog Commit => Get ack from slave, return success to client.

Loss-less semisync is in the MySQL 5.7 feature set. Facebook backported it to 5.6 and have used it in production.

MySQL 5.6 can stream binlog. Facebook patched mysqlbinlog to have `--use-semisync` so it speaks semisync proto.

Facebook uses monitoring and tailer daemons for mysqlbinlog. Booking.com has a similar idea with binlog servers.

Facebook has an agent on each MySQL server that hearbeats master connection with timeout and checks slave status.

In MySQL 5.6 GTID failover incurs downtime. Furthermore, online GTID settings are not possible until MySQL 5.7.

Facebook's slave promotion: identify slave with latest GTID => apply diff GTID to new master => slaves reconnect.

To avoid split brain you must STONITH, kill the network port, or stop semisync tailers (master waits for ack forever).

With automatic failover, it is helpful to check MySQL error codes to determine if the node is dead or just too busy.

If the master dies due to OOM and a slave is promoted it may also be killed by OOM. Slave promotion is rate limited.

Master recovery is handled by replaying binlogs instead of rebuilding the instance (binlog trimming may be necessary).

Failure scenarios tested were mysqld crash, kernel panic and reboot, various spikes and stalls, and hardware outages.

