## BINLOG SERVERS AT BOOKING.COM
_Jean-François Gagné (Senior System Engineer, Booking.com)_

Binlog Server is a daemon that downloads the binlog from the master, saves them, and then serves them to slaves.

GTIDs and intermediate masters with log-slave-updates (which slow replication) are not needed with binlog servers.

It reads and stores the data similar to mysqlbinlog but also listens on a port and speaks the MySQL slave protocol.

As the number of slaves grows the network resources can become exhausted and affect the master's performance.

The typical solution of introducting intermediate masters can create bottlenecks and add potential points of failure.

If you use DRBD for HA it requires `sync_binlog=1` and `trx_commit=1` which can slow replication and cause lag.

Binlog server failure is addressed by repointing the affected slave(s) to another healthy binlog server.

Slave promotion is easier because the usual first step is to bring all slaves to a common binlog location.

From the point of view of the binlog servers the master (promoted slave) only rebooted with a new `server_id`.

Slaves do not keep binary logs themselves. If they have binary logs it is probably evidence of an errant transaction.

Having a copy of the master binlogs allows you to build an HA solution from the uncorrupted copy of the binlogs.

A binlog server cluster consists of at least two independent binlog servers replicated from the same master.

The future path for binlog servers is to implement a distributed and resilient binlog service.

http://jfg-mysql.blogspot.com/2015/04/maxscale-binlog-server-howto-install-and-configure.html
