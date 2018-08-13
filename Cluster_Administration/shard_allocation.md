# Shard Allocation #

##  Cluster Level Shard Allocation ##
When does this happen? During:
* Initial Recovery
* Replica Allocation
* Rebalancing
* Change in nodes of the cluster

[Documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current/shards-allocation.html)

### Important notes ###
* This is mostly rebalancing (interesting in case of changing cluster size) and recovery
* These options do not manage initial shard creation (See: Hot/Warm architecture)
* Decide on targets for re-allocation (all or primaries only)
* How many concurrent shard movements? This creates strain on the cluster
