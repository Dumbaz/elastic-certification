# Shard Allocation #

##  Cluster Level Shard Allocation ##
Configuration setting

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

## Cluster Reroute ##
API

[Documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-reroute.html)

## Important nodes ##
What is this used for:
* Move specific shards to specific nodes
* Cancel allocations of replica shards
* Force the allocation of a replica shard (when it ran into max retries)

Example Code:
```
curl -X POST "localhost:9200/_cluster/reroute" -H 'Content-Type: application/json' -d'
{
    "commands" : [
        {
            "move" : {
                "index" : "test", "shard" : 0,
                "from_node" : "node1", "to_node" : "node2"
            }
        },
        {
          "allocate_replica" : {
                "index" : "test", "shard" : 1,
                "node" : "node3"
          }
        }
    ]
}
'
```

## Shard Allocation Awareness ##
Configuration setting

[Documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current/allocation-awareness.html)

When does this happen? During:
* Initial shard creation

Why do I want this?
* Different firezones in a data center
* Heterogenous server configurations (higher and lower performing)
* Hot/Warm architecture