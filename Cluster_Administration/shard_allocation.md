# Shard Allocation #

##  Cluster Level Shard Allocation ##
Configuration setting

When does this happen? During:
* Initial Recovery
* Replica Allocation
* Rebalancing
* Change in the amount of nodes of the cluster

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

## Diagnose Shard Issues, repair Cluster Health ##
1. Use Cluster Allocation Explain API. [Documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-allocation-explain.html)

```
GET /_cluster/allocation/explain
{
  "index": "myindex",
  "shard": 0,
  "primary": true,
  "current_node": "optionalNodeName"
}
```

Optional parameters:
```
?include_disk_info=true
?include_yes_decisions=true
```

Possible reasons for the failure:
* Allocation not allowed on any node
* Primary Shard that had already been defined has been lost
* Shard is still not timed out (Node may rejoin the cluster before timeout)
* Shard not allowed to exist on current node (firezones, balancing reasons) but no other node to reallocate to
* No better balancing possibility after shard moves to another node
* Rebalancing turned off
* Filters leave no valid choice for allocation
* Too many shards, not enough nodes to allocate to
* Shard data no longer exists anywhere in the cluster
* Low/high disk watermark
* Different versions of elasticsearch
* Split brain Issue

2. Further APIs to query
```
GET _cat/recovery?v
GET _cluster/health
GET _cluster/health?level=indices&pretty
_cat/shards
```


### Documentation and Blog Posts ###
[Blog: Cluster Allocation Explain API](https://www.elastic.co/blog/red-elasticsearch-cluster-panic-no-longer)
[Blog: Repair unassigned Shards](https://www.datadoghq.com/blog/elasticsearch-unassigned-shards/)
[Blog: Failed Shard Recovery](http://www.jillesvangurp.com/2015/02/18/elasticsearch-failed-shard-recovery/)