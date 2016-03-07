
## Consumer API
As of the 0.9.0 release we have added a new Java consumer to replace our
existing high-level ZooKeeper-based consumer and low-level consumer APIs. This
client is considered beta quality. To ensure a smooth upgrade path for users,
we still maintain the old 0.8 consumer clients that continue to work on an 0.9
Kafka cluster. In the following sections we introduce both the old 0.8
consumer APIs (both high-level ConsumerConnector and low-level SimpleConsumer)
and the new Java consumer API respectively.