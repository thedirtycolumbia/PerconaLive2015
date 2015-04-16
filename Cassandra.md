## INSERT CASSANDRA INTO PROD WHERE USE_CASE=?;
_Susanne Lehmann (Database Engineer, Yelp)_

If I had asked people what they wanted, they would have said faster horses. (Henry Ford)

Why move away from MySQL? Yelp has _tons_ of data, _tons_ of users, and the users are distributed all over the world.

Yelp technologies are very Python and MySQL heavy.

The driving force towards C* was multi-datacenter, scalable writes and a non-relational (k/v style) data pattern.

Chose Cassandra over other columnar databases like HBase and Riak.

Highlights of C* are no SPOF, graceful handling of failure, and horizontal scaling.

The application, in general, is read heavy (more reviews read than written) but Yelp has write heavy workloads too.

Cassandra is a wide-column database which gives it a more flexible schema than a k/v store.

Cassandra has a very strong community, which was something Yelp like about MySQL.

The first cluster setup choice was whether to deploy lots of small nodes or a few powerful nodes.

Yelp went with one cluster per service as they use service oriented architecture.

Yelp runs DataStax Community Edition on AWS with c3.8xlarge with durable writes enabled.

Yelp runs 8 node clusters, 4 in 2 distinct AWS regions spread across availability zones.

Yelp uses only ephemeral SSD storage with a replication factor of 3 per DC and the DC-aware gossip snitch.

Yelp uses the recommendations of no swap, 8GB heap size, ulimits unlimited, but tune GC as needed.

Yelp uses http://nerds.airbnb.com/smartstack-service-discovery-cloud/ (nerve + zookeeper) for service discovery.

Because Cassandra trades away the C in CAP, Yelp picked use cases that play well with eventual consistency.

Yelp's uses Cassandra for Feed Data (i.e. nearby and friends feeds) which is chronologically organized.

Cassandra is good for when you prefer Fan Out On Write instead of Fan Out On Read.

Data is denormalized into three CFs: `user_personal_feed`, `user_friend_feed`, and `user_following_feed`.

The next use case for Cassandra at Yelp is caching data with long primary keys close to the user.

Memcached was the original caching solution, but was unable to failover across datacenters effectively.

Cassandra is not as fast as with memcached but it is still fast enough.

The third use case is for storing time series data related to ads (impressions, clicks, etc).

Ad metrics are very transient, it is only need for two days. Cassandra cleans up with 48 hour TTLs.

So far, 10 services use Cassandra, but there are more in the pipeline. Yelp is happy with the addition of Cassandra.

Most of Yelp's clusters use a tunable consistency level of QUORUM, however a few clusters also use a CL of ONE.

Yelp uses virtual nodes as it makes scalability easier than with manual token management.

Data is initially written to a Memtable (similar to the buffer pool). Once full data is persisted to SSTables.

Deletes are difficult because SSTables are immutable and data is distributed. Tombstones mark deleted data.

Compaction merges keys, combines columns, evicts tombstones, consolidates SSTables, and creates new indexes.

Yelp was picking different compaction strategies per use case (STC for feed data, LC for caching, TSC for ad data).

Changing the compaction strategy is a long running process in a running cluster and can impact performance.

Cassandra comes out of the box with jmx and open source jmx trans which can be plugged into Graphite/Grafana.

Yelp does not use OpsCenter because they didn't want yet another tool to look at.

Yelp uses Sensu for alerting in general and for Cassandra as well with specific alerts using https://jolokia.org.

You still need backups in a distributed system (e.g. logical failure). Yelp uses a scripted solution for backups.

Cassandra has been relatively low maintenance, failover has been easy, and cqlsh provides a nice familiar interface.

On the other hand, Cassandra is not as mature as MySQL (e.g. needing to use netstat to see the running threads).

The hard part of Cassandra is schema design. **Determine beforehand exactly what queries you need to support!**

Also difficult is that detailed information is hard to find. MySQL is still way better documented.

Troubleshooting a distributed system is more difficult.

Adding a new datacenter or changing non-TTL data types to TTL data types can be difficult in a running system.

Looking toward the future, Yelp would like to have log aggregation and and more reporting tools for Cassandra.
