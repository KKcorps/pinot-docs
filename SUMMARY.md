# Table of contents

* [Introduction](README.md)

## Basics

* [Concepts](basics/concepts.md)
* [Architecture](basics/architecture.md)
* [Components](basics/components/README.md)
  * [Cluster](basics/components/cluster.md)
  * [Controller](basics/components/controller.md)
  * [Broker](basics/components/broker.md)
  * [Server](basics/components/server.md)
  * [Minion](basics/components/minion.md)
  * [Tenant](basics/components/tenant.md)
  * [Table](basics/components/table.md)
  * [Segment](basics/components/segment.md)
* [Getting started](basics/getting-started/README.md)
  * [Running Pinot locally](basics/getting-started/running-pinot-locally.md)
  * [Running Pinot in Docker](basics/getting-started/running-pinot-in-docker.md)
  * [Running Pinot in Kubernetes](basics/getting-started/kubernetes-quickstart.md)
  * [Public cloud examples](basics/getting-started/public-cloud-examples/README.md)
    * [Running on Azure](basics/getting-started/public-cloud-examples/azure-quickstart.md)
    * [Running on GCP](basics/getting-started/public-cloud-examples/gcp-quickstart.md)
    * [Running on AWS](basics/getting-started/public-cloud-examples/aws-quickstart.md)
  * [Manual cluster setup](basics/getting-started/advanced-pinot-setup.md)
  * [Batch import example](basics/getting-started/pushing-your-data-to-pinot.md)
  * [Stream ingestion example](basics/getting-started/pushing-your-streaming-data-to-pinot.md)
  * [Frequently Asked Questions \(FAQs\)](basics/getting-started/frequent-questions.md)
* [Data import](basics/data-import/README.md)
  * [Batch Ingestion](basics/data-import/batch-ingestion/README.md)
    * [Spark](basics/data-import/batch-ingestion/spark.md)
    * [Hadoop](basics/data-import/batch-ingestion/hadoop.md)
  * [Stream ingestion](basics/data-import/pinot-stream-ingestion/README.md)
    * [Apache Kafka](basics/data-import/pinot-stream-ingestion/import-from-apache-kafka.md)
  * [File systems](basics/data-import/pinot-file-system/README.md)
    * [Amazon S3](basics/data-import/pinot-file-system/amazon-s3.md)
    * [Azure Data Lake Storage](basics/data-import/pinot-file-system/import-from-adls-azure.md)
    * [HDFS](basics/data-import/pinot-file-system/import-from-hdfs.md)
    * [Google Cloud Storage](basics/data-import/pinot-file-system/import-from-gcp.md)
  * [Input formats](basics/data-import/pinot-input-formats.md)
* [Feature guides](basics/features/README.md)
  * [Pinot data explorer](basics/features/exploring-pinot.md)
  * [Text search support](basics/features/text-search-support.md)
  * [Indexing](basics/features/indexing.md)
* [Releases](basics/releases/README.md)
  * [0.4.0](basics/releases/0.4.0.md)
  * [0.3.0](basics/releases/0.3.0.md)
  * [0.2.0](basics/releases/0.2.0.md)
  * [0.1.0](basics/releases/1.0.md)
* [Recipes](basics/recipes/README.md)
  * [GitHub Events Stream](basics/recipes/github-events-stream.md)

## Configuration Reference

* [Cluster](configuration-reference/cluster.md)
* [Controller](configuration-reference/controller.md)
* [Broker](configuration-reference/broker.md)
* [Server](configuration-reference/server.md)
* [Table](configuration-reference/table.md)
* [Schema](configuration-reference/schema.md)

## For Users <a id="users"></a>

* [Query](users/user-guide-query/README.md)
  * [Pinot Query Language \(PQL\)](users/user-guide-query/pinot-query-language/README.md)
    * [Unique Counting](users/user-guide-query/pinot-query-language/how-to-handle-unique-counting.md)
* [API](users/api/README.md)
  * [Querying Pinot](users/api/querying-pinot-using-standard-sql/README.md)
    * [Response Format](users/api/querying-pinot-using-standard-sql/response-format.md)
  * [Pinot Rest Admin Interface](users/api/pinot-rest-admin-interface.md)
* [Clients](users/clients/README.md)
  * [JDBC](users/clients/jdbc.md)
  * [Java](users/clients/java.md)
  * [Python](users/clients/python.md)
  * [Golang](users/clients/golang.md)

## For Developers <a id="developers"></a>

* [Basics](developers/developers-and-contributors/README.md)
  * [Extending Pinot](developers/developers-and-contributors/extending-pinot/README.md)
    * [Writing Custom Aggregation Function](developers/developers-and-contributors/extending-pinot/custom-aggregation-function.md)
    * [Segment Fetchers](developers/developers-and-contributors/extending-pinot/segment-fetchers.md)
  * [Contribution Guidelines](developers/developers-and-contributors/contribution-guidelines.md)
  * [Code Setup](developers/developers-and-contributors/code-setup.md)
  * [Code Modules and Organization](developers/developers-and-contributors/code-modules-and-organization.md)
  * [Update Documentation](developers/developers-and-contributors/update-document.md)
* [Advanced](developers/advanced/README.md)
  * [Data Ingestion Overview](developers/advanced/data-ingestion.md)
  * [Ingestion Transforms](developers/advanced/ingestion-level-transformations.md)
  * [Advanced Pinot Setup](developers/advanced/advanced-pinot-setup.md)
* [Tutorials](developers/tutorials/README.md)
  * [Pinot Architecture](developers/tutorials/pinot-architecture.md)
  * [Store Data](developers/tutorials/store-data/README.md)
    * [Batch Tables](developers/tutorials/store-data/offline-tables.md)
    * [Streaming Tables](developers/tutorials/store-data/realtime-tables.md)
  * [Ingest Data](developers/tutorials/pinot-connectors/README.md)
    * [Batch](developers/tutorials/pinot-connectors/batch/README.md)
      * [Creating Pinot Segments](developers/tutorials/pinot-connectors/batch/create-pinot-segments.md)
      * [HDFS](developers/tutorials/pinot-connectors/batch/hdfs.md)
      * [AWS S3](developers/tutorials/pinot-connectors/batch/s3.md)
      * [Azure Storage](developers/tutorials/pinot-connectors/batch/azure.md)
      * [Google Cloud Storage](developers/tutorials/pinot-connectors/batch/gcs.md)
    * [Streaming](developers/tutorials/pinot-connectors/streaming/README.md)
      * [Creating Pinot Segments](developers/tutorials/pinot-connectors/streaming/create-pinot-segments.md)
      * [Kafka](developers/tutorials/pinot-connectors/streaming/kafka.md)
      * [Azure EventHub](developers/tutorials/pinot-connectors/streaming/eventhub.md)
      * [Amazon Kinesis](developers/tutorials/pinot-connectors/streaming/kinesis.md)
      * [Google Pub/Sub](developers/tutorials/pinot-connectors/streaming/google-pub-sub.md)
* [Design Documents](developers/design-documents.md)

## For Operators <a id="operators"></a>

* [Basics](operators/operating-pinot/README.md)
  * [Setup cluster](operators/operating-pinot/setup-cluster.md)
  * [Setup table](operators/operating-pinot/setup-table.md)
  * [Setup ingestion](operators/operating-pinot/setup-ingestion.md)
  * [Access Control](operators/operating-pinot/access-control.md)
  * [Monitoring](operators/operating-pinot/monitoring.md)
  * [Tuning](operators/operating-pinot/tuning/README.md)
    * [Realtime](operators/operating-pinot/tuning/realtime.md)
    * [Routing](operators/operating-pinot/tuning/routing.md)
* [Tutorials](operators/tutorials/README.md)
  * [Build Docker Images](operators/tutorials/build-docker-images.md)
  * [Running Pinot in Production](operators/tutorials/running-pinot-in-production.md)
  * [Kubernetes Deployment](operators/tutorials/deployment-pinot-on-kubernetes.md)
  * [Amazon EKS \(Kafka\)](operators/tutorials/non-eks-to-eks.md)
  * [Amazon MSK \(Kafka\)](operators/tutorials/how-to-connect-pinot-with-amazon-managed-streaming-for-apache-kafka-amazon-msk.md)
  * [Batch Data Ingestion In Practice](operators/tutorials/batch-data-ingestion-in-practice.md)

## RESOURCES <a id="community-1"></a>

* [Community](community-1/community.md)
* [Blogs](community-1/blogs.md)
* [Presentations](community-1/blogs-and-presentations.md)
* [Videos](community-1/videos.md)

## Integrations

* [ThirdEye](integrations/thirdeye.md)
* [Superset](integrations/superset.md)
* [Presto](integrations/presto.md)

## PLUGINS

* [Plugin Architecture](plugins/plugin-architecture.md)
* [Write Custom Plugins](plugins/write-custom-plugins/README.md)
  * [Input Format Plugin](plugins/write-custom-plugins/record-reader.md)
  * [Filesystem Plugin](plugins/write-custom-plugins/pluggable-storage.md)
  * [Batch Segment Fetcher Plugin](plugins/write-custom-plugins/write-your-batch.md)
  * [Stream Ingestion Plugin](plugins/write-custom-plugins/write-your-stream.md)

---

* [Administration](operating-pinot.md)

