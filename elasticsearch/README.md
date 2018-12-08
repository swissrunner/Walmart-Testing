
## Overview

Search is dependent on three services
1. [Elasticsearch](elasticsearch-details)
    * Store and query data
2. ALM
    * Queries Elasticsearch through a jar called `waterbear`
    * Publishes Object Change Messages (OCMs) onto Kafka that gopher consumes
3. [Gopher](#gopher-details)
    * Consumes OCMs from Kafka and indexes data into Elasticsearch
    * Handles the mappings/languages/configuration in Elasticsearch

## Elasticsearch details

### Metrics

You can see elasticsearch related metrics [here](https://grafana.rallydev.com/dashboard/db/elasticsearch)

### Alerts

#### Elasticsearch replication

This alert compares the difference in number of documents between the two datacenters. If the difference is to large the alert will go off. This can be caused by a couple
circumstances:
* gopher/gopher-replica is down in one datacenter
    * No need to do anything in this case. When gopher/gopher-replica comes back up it will catch up on all messages published
* Data is missing in one datacenter
    * Usually caused by a subscription/data only being indexed in one of the datacenters
    * See [Specific customer data issues](#specific-customer-data-issues)
    * Work with OUTS to determine what data is missing

#### Elasticsearch updates

This alert checks that a specific story is being continuously updated. The `ocm-canary` service updates the story. If this alert goes off it is most likely:
* gopher/gopher-replica is down or is unable to keep up
    * Most likely the case if no data is making it into Elasticsearch at all (can see this under "document creation rate" on grafana)
    * Manually change a story, and see if it is reflected in search results within ~10 seconds
* ocm-canary is having issues
    * Most likely the case if other data is making it into elasticsearch

## Gopher details

Gopher takes OCMs from Kafka and indexes them into Elasticsearch. It also does some work on startup to change settings in Elasticsearch pertaining to how data is indexed.

### Metrics

Metrics for gopher are found in Sysdig. Dashboards are found:
* [here for gopher](https://app.sysdigcloud.com/#/dashboards/22683)
* [here for gopher-replica](https://app.sysdigcloud.com/#/dashboards/22684)

### Gopher-replica

Gopher-replica is a second instance of gopher that runs in each datacenter. It points to the Kafka in the opposite data center in order to keep Elasticsearch in sync.
For example, qs-gopher-replica will read data from qd-kafka, and write data into qs-elasticsearch to keep it in sync with qd-elasticsearch.

### What happens if gopher goes down?

If gopher goes down for any reason data will stop being indexed into Elasticsearch. When gopher comes back up, it will start indexing data from where it left off.
If gopher is down for longer than the Kafka retention period (4 days), then some data will have been missed and it will be necessary to index the missing data from
ALM, see [Recent data missing](#recent-data-missing).

## Incident Recovery

### Recent data missing

In ALM it is possible to generate OCMs to reindex data for the last `x` days. To do this:
* Log in as an admin user
* Navigate to Tools > OCM Generation Jobs
* Under "Generate OCM for Artifacts (Reindexing Elasticsearch)" fill in the field for "Number of days back to index" as however long ago the incident began
* Click the "Generate OCM" button under the "Number of days back to index" field

### Specific customer data issues

In ALM it is possible to generate OCMs to reindex data for the last `x` days. To do this:
* Log in as an admin user
* Navigate to Tools > OCM Generation Jobs
* Under "Generate OCM for Artifacts (Reindexing Elasticsearch)" fill in the subscription id updated in the field for "Subscription ID"
* Click the "Generate OCM" button under the "Subscription ID" field

NOTE: If this is a very big subscription it may take several hours to generate all the necessary OCMs.

### Disaster Recovery

This section is for when we lose an entire data center or lose an entire cluster (whole cluster, not just a single node!).

**Work with OUTS during recovery.  They can turn off Gopher on a per-DC basis, and you don't want to re-enable it in the rebuild data center too early!**

The following steps will need to be done to get the elasticsearch cluster back up

1. Turn off gopher
2. Bring the elasticsearch cluster back online
3. Make sure there are no indices `curl -XGET 'http://qs-elasticsearch-01.rally.prod:9200/_cat/indices'
    * Work with OUTS to make sure the mappings are correct if there are indices
    * If the mappings are not correct delete the indices `curl -XDELETE http://qs-elasticsearch-01.rally.prod:9200/.index-manager,artifact-*`
4. Turn gopher back on. This will create artifact indices with the correct mappings
5. Move data into elasticsearch from the other datacenter as described below

### Indexing data using jar

The fastest way to restore all data (~12-15 hours) is to generate all OCMs using a special indexing jar.
See the ansible script [here](https://github.com/RallySoftware/ops_ansible/blob/master/playbooks/reindex.yml).

If this script does not work for some reason, you can use the _reindex api in Elasticsearch described below.

### Moving data

We and OUTS have agreed to use the [reindex API](https://www.elastic.co/guide/en/elasticsearch/reference/master/docs-reindex.html) to push the index from one data center to another in the event of a disater.

Here is the command we used to replicate the English index from QD to QS, make sure to use the highest artifact index version:

```
curl -XPOST 'http://qs-elasticsearch-01.rally.prod:9200/_reindex?wait_for_completion=false' -H 'Content-Type: application/json' -d'
> {
>   "conflicts": "proceed",
>   "source": {
>     "remote": {
>       "host": "http://qd-elasticsearch-01.rally.prod:9200"
>     },
>     "index": "artifact-16_english"
>   },
>   "dest": {
>     "index": "artifact-16_english",
>     "version_type": "external_gte"
>   }
> }
> '
```

When tested on July 26th, 2017 moving one language from qd -> qs took 19.5 hours. You will need to replicate the other indexes too. This can be done more quickly within one datacenter after english is copied across:

```
curl -XPOST 'http://qs-elasticsearch-01.rally.prod:9200/_reindex?wait_for_completion=false&slices=50' -H 'Content-Type: application/json' -d'
> {
>   "conflicts": "proceed",
>   "source": {
>     "index": "artifact-16_english"
>   },
>   "dest": {
>     "index": "artifact-16_german",
>     "version_type": "external_gte"
>   }
> }
> '
```

You can control parallelization for non-remote reindexing using the url parameter `slices=x`

You can use the [task management API](https://www.elastic.co/guide/en/elasticsearch/reference/current/tasks.html) to view the status of the replication task.  After replication is complete, contact OUTS, they will re-enable Gopher and can do some magic to make sure it replays any messages that were lost between the replication ending and Gopher starting.

List indexes:
```curl -XGET 'qd-elasticsearch-01.rally.prod:9200/_cat/indices?v'```
