# Table

## Offline Table Config

Here's an example table config for an offline table

{% code title="pinot-table-offline.json" %}
```javascript
"OFFLINE": {
    "tableName": "pinotTable", 
    "tableType": "OFFLINE",
    "quota": {
      "maxQueriesPerSecond": 300, 
      "storage": "140G"
    }, 
    "routing": {
      "segmentPrunerType": "partition", 
      "instanceSelectorType": "replicaGroup"
    }, 
    "segmentsConfig": {
      "schemaName": "pinotTable", 
      "timeColumnName": "daysSinceEpoch", 
      "timeType": "DAYS",
      "replication": "3", 
      "retentionTimeUnit": "DAYS", 
      "retentionTimeValue": "365", 
      "segmentPushFrequency": "DAILY", 
      "segmentPushType": "APPEND" 
    }, 
    "tableIndexConfig": { 
      "invertedIndexColumns": ["foo", "bar", "moo"], 
      "createInvertedIndexDuringSegmentGeneration": false, 
      "sortedColumn": [],
      "bloomFilterColumns": [],
      "starTreeIndexConfigs": [],
      "noDictionaryColumns": [],
      "rangeIndexColumns": [],
      "onHeapDictionaryColumns": [], 
      "varLengthDictionaryColumns": [],
      "loadMode": "MMAP", 
      "columnMinMaxValueGeneratorMode": null
    },  
    "tenants": {
      "broker": "myBrokerTenant", 
      "server": "myServerTenant"
    },
    "ingestionConfig": {
      "filterConfig": {
        "filterFunction": "Groovy({foo == \"VALUE1\"}, foo)"
      },
      "transformConfigs": [{
        "columnName": "bar",
        "transformFunction": "lower(moo)"
      },
      {
        "columnName": "hoursSinceEpoch",
        "transformFunction": "toEpochHours(millis)"
      }]
    }
    "metadata": {
      "customConfigs": {
        "key": "value", 
        "key": "value"
      }
    }
  }
}
```
{% endcode %}

We will now discuss each section of the table config in detail. 

### Top level fields

| Top level field | Description |
| :--- | :--- |
| **tableName** | Specifies the name of the table. Should only contain alpha-numeric characters, hyphens \(‘-‘\), or underscores \(‘_’\).  \(Using a double-underscore \(‘\__’\) is not allowed and reserved for other features within Pinot\) |
| **tableType** | Defines the table type - `OFFLINE` for offline table,  `REALTIME` for realtime table.  A hybrid table is essentially 2 table configs one of each type, with the same table name. |
| **quota** | This section defines properties related to quotas, such as storage quota and query quota. For more details scroll down to [quota](../basics/components/table.md#quota) |
| **routing** | This section defines the properties related to configuring how the broker selects the servers to route, and how segments can be pruned by the broker based on segment metadata. For more details, scroll down to [routing](../basics/components/table.md#routing) |
| **segmentsConfig** | This section defines the properties related to the segments of the table, such as segment push frequency, type, retention, schema, time column etc. For more details scroll down to [segmentsConfig](https://docs.pinot.apache.org/basics/components/table#segmentsconfig-1) |
| **tableIndexConfig** | This section helps configure indexing and dictionary encoding related information for the Pinot table. For more details head over to [tableIndexConfig](https://docs.pinot.apache.org/basics/components/table#tableindexconfig-1) |
| **tenants** | Define the server and broker tenant used for this table. More details about tenant can be found in [Tenant](../basics/components/tenant.md). |
| **ingestionConfig** | This section defines the configs needed for ingestion level transformations. More details in[ Ingestion Level Transformations](../developers/advanced/ingestion-level-transformations.md) |
| **metadata** | This section is for keeping custom configs, which are expressed as key value pairs. |

### Second level fields

#### quota

<table>
  <thead>
    <tr>
      <th style="text-align:left">quota fields</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">storage</td>
      <td style="text-align:left">The maximum storage space the table is allowed to use, before replication.
        For example, in the above table, the storage is 140G and replication is
        3. Therefore, the maximum storage the table is allowed to use is 140*3=420G.
        The space used by the table is calculated by adding up the sizes of all
        segments from every server hosting this table. Once this limit is reached,
        offline segment push throws a <code>403</code> exception with message, <code>Quota check failed for segment: segment_0 of table: pinotTable</code>.</td>
    </tr>
    <tr>
      <td style="text-align:left">maxQueriesPerSecond</td>
      <td style="text-align:left">
        <p>The maximum queries per second allowed to execute on this table. If query
          volume exceeds this, a <code>429</code> exception with message,<code>Request 123 exceeds query quota for table:pinotTable, query:select count(*) from pinotTable</code>
        </p>
        <p> will be sent, and a BrokerMetric <code>QUERY_QUOTA_EXCEEDED</code> will
          be recorded. The application should build an exponential backoff and retry
          mechanism to react to this exception.</p>
      </td>
    </tr>
  </tbody>
</table>

#### routing

| routing fields | description |
| :--- | :--- |
| segmentPrunerType | The segment pruner prunes the selected segments based on the query. Supported values currently are  `partition`  -  prunes segments based on the partition metadata stored in zookeeper. By default, there is no pruner. For mode details on how to configure this check out [Querying All Segments](../operators/operating-pinot/tuning/routing.md#querying-all-segments) |
| instanceSelectorType | The instance selector selects server instances to serve the query based on selected segments. Supported values are `balanced` - balances the number of segments served by each selected instance. Default. `replicaGroup` -  instance selector for replica group routing strategy. For more details on how to configure this check out [Querying All Servers](../operators/operating-pinot/tuning/routing.md#querying-all-servers) |

#### segmentsConfig

| segmentsConfig field | Description |
| :--- | :--- |
| schemaName | Name of the schema associated with the table |
| timeColumnName | The name of the time column for this table. This must match with the time column name in the schema. This is mandatory for tables with push type `APPEND`, optional for `REFRESH.` timeColumnName along with timeColumnType is used to manage segment retention and time boundary for offline vs realtime. |
| timeColumnType | The time column type of the time column. eg. MILLISECONDS, SECONDS, MINUTES, HOURS, DAYS |
| replication | Number of replicas |
| retentionTimeUnit | Unit for the retention. e.g. HOURS, DAYS. This in combination with retentionTimeValue decides the duration for which to retain the segments e.g. `365 DAYS` in the example means that segments containing data older than 365 days will be deleted periodically. This is done by the `RetentionManager` Controller periodic task. By default, no retention is set. |
| retentionTimeValue | A numeric value for the retention. This in combination with retentionTimeUnit decides the duration for which to retain the segments  |
| segmentPushType | This can be either `APPEND` - new data segments pushed periodically, to append to the existing data eg. daily or hourly `REFRESH` - the entire data is replaced every time during a data push. Refresh tables have no retention. |
| segmentPushFrequency | The cadence at which segments are pushed eg. `HOURLY`, `DAILY`  |

#### tableIndexConfig

| tableIndexConfig fields | Description |
| :--- | :--- |
| invertedIndexColumns | The list of columns that inverted index should be created on. The name of columns should match the schema. e.g. in the table above, inverted index has been created on 3 columns `foo`, `bar`, `moo`  |
| createInvertedIndexDuringSegmentGeneration | Boolean to indicate whether to create inverted indexes during the segment creation. By default, false i.e. inverted indexes are created when the segments are loaded on the server |
| sortedColumn | The column which is sorted in the data and hence will have a sorted index. This does not need to be specified for the offline table, as the segment generation job will automatically detect the sorted column in the data and create a sorted index for it. |
| bloomFilterColumns | The list of columns to apply bloom filter on. The names of the columns should match the schema. For more details about using bloom filters refer to [Bloom Filter for Dictionary](https://apache-pinot.gitbook.io/apache-pinot-cookbook/operating-pinot/tuning/routing#bloom-filter-for-dictionary). |
| rangeIndexColumns | The list of columns that range index should be created on.  Typically used for numeric columns and mostly on metrics. e.g. `select count(*) from T where latency > 3000` will be faster if you enable range index for **latency**  |
| starTreeIndexConfigs | The list of star-tree indexing configs for creating star-tree indexes. For more details on how to configure this, go to [Star-tree](https://apache-pinot.gitbook.io/apache-pinot-cookbook/indexing#index-generation-configuration) |
| enableDefaultStarTree | Boolean to indicate whether to create a default star-tree index for the segment. For more details about this, go to [Star-tree](https://docs.pinot.apache.org/basics/features/indexing#default-index-generation-configuration) |
| enableDynamicStarTreeCreation | Boolean to indicate whether to allow creating star-tree when server loads the segment. Star-tree creation could potentially consume a lot of system resources, so this config should be enabled when the servers  have the free system resources to create the star-tree. |
| noDictionaryColumns | The set of columns that should not be dictionary encoded. The name of columns should match the schema. NoDictionary dimension columns are [Snappy](https://github.com/xerial/snappy-java/blob/master/README.md) compressed, while the metrics are not compressed. |
| onHeapDictionaryColumns | The list of columns for which the dictionary should be created on heap |
| varLengthDictionaryColumns | The list of columns for which the variable length dictionary needs to be enabled in offline segments. This is only valid for string and bytes columns and has no impact for columns of other data types. |
| loadMode | Indicates how the segments will be loaded onto the server `heap` - load data directly into direct memory `mmap` - load data segments to off-heap memory |
| columnMinMaxValueGeneratorMode | Generate min max values for columns. Supported values are `NONE` - do not generate for any columns `ALL` - generate for all columns `TIME` - generate for only time column `NON_METRIC` - generate for time and dimension columns |



## Realtime Table Config

Here's an example table config for a realtime table. **All the fields from the offline table config are valid for the realtime table**. Additionally, realtime tables use s**ome extra fields**.

{% code title="pinot-table-realtime.json" %}
```javascript
"REALTIME": { 
    "tableName": "pinotTable", 
    "tableType": "REALTIME", 
    "segmentsConfig": {
      "schemaName": "pinotTable", 
      "timeColumnName": "daysSinceEpoch", 
      "timeType": "DAYS",
      "replicasPerPartition": "3", 
      "retentionTimeUnit": "DAYS", 
      "retentionTimeValue": "5", 
      "completionConfig": {
        "completionMode": "DOWNLOAD"
      } 
    }, 
    "tableIndexConfig": { 
      "invertedIndexColumns": ["foo", "bar", "moo"], 
      "sortedColumn": ["column1"],
      "noDictionaryColumns": ["metric1", "metric2"],
      "loadMode": "MMAP", 
      "aggregateMetrics": true, 
      "streamConfigs": {
        "realtime.segment.flush.threshold.size": "0",
        "realtime.segment.flush.threshold.time": "24h",
        "realtime.segment.flush.desired.size": "150M",
        "stream.kafka.broker.list": "XXXX", 
        "stream.kafka.consumer.factory.class.name": "XXXX", 
        "stream.kafka.consumer.prop.auto.offset.reset": "largest", 
        "stream.kafka.consumer.type": "XXXX", 
        "stream.kafka.decoder.class.name": "XXXX", 
        "stream.kafka.decoder.prop.schema.registry.rest.url": "XXXX", 
        "stream.kafka.decoder.prop.schema.registry.schema.name": "XXXX", 
        "stream.kafka.hlc.zk.connect.string": "XXXX", 
        "stream.kafka.topic.name": "XXXX", 
        "stream.kafka.zk.broker.url": "XXXX", 
        "streamType": "kafka"
      }  
    },  
    "tenants": {
      "broker": "myBrokerTenant", 
      "server": "myServerTenant", 
      "tagOverrideConfig": {}
    },
    "metadata": {
    }
}
```
{% endcode %}

We will now discuss the sections have some behavior differences for realtime tables.

### segmentsConfig

**replicasPerPartition**  
The number of replicas per partition for the realtime stream

**completionConfig**  
Holds information related to realtime segment completion. There is just one field in this config as of now, which is the `completionMode`. The value of the `completioMode` decides how non-committers servers should replace the in-memory segment during realtime segment completion. By default, if the in memory segment in the non-winner server is equivalent to the committed segment, then the non-committer server builds and replaces the segment, else it download the segment from the controller.

Currently, the supported value for `completionMode` is

* `DOWNLOAD`: In certain scenarios, segment build can get very memory intensive. It might become desirable to enforce the non-committer servers to just download the segment from the controller, instead of building it again. Setting this completionMode ensures that the non-committer servers always download the segment.

 For more details on why this is needed, check out [Completion Config](https://apache-pinot.gitbook.io/apache-pinot-cookbook/operating-pinot/tuning/realtime#controlling-segment-build-vs-segment-download-on-realtime-servers)

**peerSegmentDownloadScheme**

The value can be _http_ or _https_**.**  When Pinot servers fail to download segments from the segment deep store during the segment completion protocol of realtime time ingestion, they check this field's value and if it exists, it can use the configured scheme \(_http_ or _https_\) to initialize the corresponding segment fetcher to download the segment from peer Pinot servers.

For more details about peer segment download during realtime ingestion, please refer to this design doc about [by passing deep store for segment completion](https://cwiki.apache.org/confluence/display/PINOT/By-passing+deep-store+requirement+for+Realtime+segment+completion#BypassingdeepstorerequirementforRealtimesegmentcompletion-Configchange).

### tableIndexConfig

**sortedColumn**  
Indicates the column which should be sorted when creating the realtime segment

**aggregateMetrics**  
Aggregate the realtime stream data as it is consumed, where applicable, in order to reduce segment sizes. We sum the metric column values of all rows that have the same value for dimension columns and create one row in a realtime segment for all such rows. This feature is only available on REALTIME tables. Only supported aggregation right now is `sum`. Also note that for this to work:

* All metrics should be listed in `noDictionaryColumns` .
* There should not be any multi value dimensions.
* All dimension columns are treated to have dictionary, even if they appear as `noDictionaryColumns` in the tableConfig.

**streamConfigs**  
This section is where the bulk of settings specific to the realtime stream and consumption are found. This section is specific to tables of type `REALTIME` and is ignored if the table type is any other. See section on [Ingesting Realtime Data](https://apache-pinot.readthedocs.io/en/latest/architecture.html#ingesting-realtime-data) for an overview of how realtime ingestion works.

Here is a minimal example of what the `streamConfigs` section may look like:

```text
"streamConfigs" : {
  "realtime.segment.flush.threshold.size": "0",
  "realtime.segment.flush.threshold.time": "24h",
  "realtime.segment.flush.desired.size": "150M",
  "streamType": "kafka",
  "stream.kafka.consumer.type": "LowLevel",
  "stream.kafka.topic.name": "ClickStream",
  "stream.kafka.consumer.prop.auto.offset.reset" : "largest"
}
```

The `streamType` field is mandatory. In this case, it is set to `kafka`. StreamType of `kafka` is supported natively in Pinot. You can use default decoder classes and consumer factory classes. Pinot allows you to use other stream types with their own consumer factory and decoder classes \(or, even other decoder and consumer factory for `kafka` if your installation formats kafka messages differently\). See [Stream Ingestion Plugin](../plugins/write-custom-plugins/write-your-stream.md)

There are some configurations that are generic to all stream types, and others that are specific to stream types.

#### Configuration generic to all stream types

* `realtime.segment.flush.threshold.size`: Maximum number of rows to consume before persisting the consuming segment.

  > Note that in the example above, it is set to 0. In this case, Pinot automatically computes the row limit using the value of realtime.segment.flush.desired.size described below. If the consumer type is HighLevel, then this value will be the maximum per consuming segment. If the consumer type is LowLevel then this value will be divided across all consumers being hosted on any one pinot-server.
  >
  > Default is 5000000.

* `realtime.segment.flush.threshold.time`: Maximum elapsed time after which a consuming segment should be persisted.

  > The value can be set as a human readable string, such as `"1d"`, `"4h30m"`, etc. This value should be set such that it is not below the retention of messages in the underlying stream, but is not so long that it may cause the server to run out of memory.
  >
  > Default is `"6h"`

* `realtime.segment.flush.desired.size`: Desired size of the completed segments.

  > This setting is supported only if consumer type is set to `LowLevel`. This value can be set as a human readable string such as `"150M"`, or `"1.1G"`, etc. This value is used when `realtime.segment.flush.threshold.size` is set to 0. Pinot learns and then estimates the number of rows that need to be consumed so that the persisted segment is approximately of this size. The learning phase starts by setting the number of rows to 100,000 \(can be changed with the setting `realtime.segment.flush.autotune.initialRows`\). and increasing to reach the desired segment size. Segment size may go over the desired size significantly during the learning phase. Pinot corrects the estimation as it goes along, so it is not guaranteed that the resulting completed segments are of the exact size as configured. You should set this value to optimize the performance of queries \(i.e. neither too small nor too large\)
  >
  > Default is `"200M"`

* `realtime.segment.flush.autotune.initialRows`: Initial number of rows for learning.

  > This value is used only if `realtime.segment.flush.threshold.size` is set o `0` and the consumer type is `LowLevel`. See `realtime.segment.flush.desired.size` above.
  >
  > Default is `"100K"`

#### Configuration specific to stream types

All of these configuration items have the prefix `stream.<streamType>`. In the example above, the prefix is `stream.kafka`.

Important ones to note here are:

* `stream.kafka.consumer.type`: This should have a value of `LowLevel` \(recommended\) or `HighLevel`.
* `stream.kafka.topic.name`: Name of the topic from which to consume.
* `stream.kafka.consumer.prop.auto.offset.reset`: Indicates where to start consumption from in the stream.

  > If the consumer type is `LowLevel`, This configuration is used only when the table is first provisioned. In `HighLevel` consumer type, it will also be used when new servers are rolled in, or when existing servers are replaced with new ones. You can specify values such as `smallest` or `largest`, or even `3d` if your stream supports it. If you specify `largest`, the consumption starts from the most recent events in the data stream. This is the recommended way to create a new table. If you specify `smallest` then the consumption starts from the earliest event available in the data stream.

All the configurations that are prefixed with the _streamType_ are expected to be used by the underlying stream. So, you can set any of the configurations described in the [Kafka configuraton page](https://kafka.apache.org/documentation/#consumerconfigs) can be set using the prefix `stream.kafka` and Kafka should pay attention to it.

More options are explained in the [Pluggable Streams](https://apache-pinot.readthedocs.io/en/latest/pluggable_streams.html#pluggable-streams) section.

### tenants

Similar to the offline table, this section defines the server and broker tenant used for this table. More details about tenant can be found in [Tenant](../basics/components/tenant.md).

**tagOverrideConfig**

A `tagOverrideConfig` can be added under the `tenants` section for realtime tables, to override tags for consuming and completed segments. For example:

```javascript
  "broker": "brokerTenantName",
  "server": "serverTenantName",
  "tagOverrideConfig" : {
    "realtimeConsuming" : "serverTenantName_REALTIME"
    "realtimeCompleted" : "serverTenantName_OFFLINE"
  }
}
```

In the above example, the consuming segments will still be assigned to `serverTenantName_REALTIME` hosts, but once they are completed, the segments will be moved to `serverTeantnName_OFFLINE`. It is possible to specify the full name of _any_ tag in this section \(so, for example, you could decide that completed segments for this table should be in pinot servers tagged as `allTables_COMPLETED`\). Refer to [Moving Completed Segments](https://apache-pinot.gitbook.io/apache-pinot-cookbook/operating-pinot/tuning/realtime#moving-completed-segments-to-different-hosts) section for learning more about this config.

## Hybrid Table Config

A hybrid table is simply a table composed of 2 tables, 1 of type offline and 1 of type realtime, which share the same name. In such a table, offline segments may be pushed periodically \(say, once a day\). The retention on the offline table can be set to a high value \(say, a few years\) since segments are coming in on a periodic basis, whereas the retention on the realtime part can be small \(say, a few days\). Once an offline segment is pushed to cover a recent time period, the brokers automatically switch to using the offline table for segments in _that_ time period, and use realtime table only to cover later segments for which offline data may not be available yet.

Here's a sample table config for a hybrid table.

{% code title="pinot-table-hybrid.json" %}
```javascript
"OFFLINE": {
    "tableName": "pinotTable", 
    "tableType": "OFFLINE", 
    "segmentsConfig": {
      ... 
    }, 
    "tableIndexConfig": { 
      ... 
    },  
    "tenants": {
      "broker": "myBrokerTenant", 
      "server": "myServerTenant"
    },
    "metadata": {
      ...
    }
  },
  "REALTIME": { 
    "tableName": "pinotTable", 
    "tableType": "REALTIME", 
    "segmentsConfig": {
      ...
    }, 
    "tableIndexConfig": { 
      ... 
      "streamConfigs": {
        ...
      },  
    },  
    "tenants": {
      "broker": "myBrokerTenant", 
      "server": "myServerTenant"
    },
    "metadata": {
    ...
    }
  }
}
```
{% endcode %}

Note that creating a hybrid table has to be done in 2 separate steps of creating an offline and realtime table individually.

