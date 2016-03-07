### 1.5 Upgrading From Previous Versions

#### Upgrading from 0.8.x or 0.9.x to 0.10.0.0

0.10.0.0 has potential breaking changes (please review before upgrading) and
there may be a performance impact during the upgrade. Because new protocols
are introduced, it is important to upgrade your Kafka clusters before
upgrading your clients.

**For a rolling upgrade:**

  1. Update server.properties file on all brokers and add the following property: inter.broker.protocol.version=CURRENT_KAFKA_VERSION (e.g. 0.8.2 or 0.9.0.0). We recommend that users set message.format.version=CURRENT_KAFKA_VERSION as well to avoid a performance regression during upgrade. See potential performance impact during upgrade for the details. 
  2. Upgrade the brokers. This can be done a broker at a time by simply bringing it down, updating the code, and restarting it. 
  3. Once the entire cluster is upgraded, bump the protocol version by editing inter.broker.protocol.version and setting it to 0.10.0.0. 
  4. Restart the brokers one by one for the new protocol version to take effect. 

**Note:** If you are willing to accept downtime, you can simply take all the brokers down, update the code and start all of them. They will start with the new protocol by default. 

**Note:** Bumping the protocol version and restarting can be done any time after the brokers were upgraded. It does not have to be immediately after. 

##### Potential performance impact during upgrade to 0.10.0.0

The message format in 0.10.0 includes a new timestamp field and uses relative
offsets for compressed messages. The on disk message format can be configured
through message.format.version in the server.properties file. The default on-
disk message format is 0.10.0. If a consumer client is on a version before
0.10.0.0, it only understands message formats before 0.10.0. In this case, the
broker is able to convert messages from the 0.10.0 format to an earlier format
before sending the response to the consumer on an older version. However, the
broker can't use zero-copy transfer in this case. To avoid such message
conversion before consumers are upgraded to 0.10.0.0, one can set the message
format to e.g. 0.9.0 when upgrading the broker to 0.10.0.0. This way, the
broker can still use zero-copy transfer to send the data to the old consumers.
Once most consumers are upgraded, one can change the message format to 0.10.0
on the broker.

For clients that are upgraded to 0.10.0.0, there is no performance impact.

**Note:** By setting the message format version, one certifies that all existing messages are on or below that message format version. Otherwise consumers before 0.10.0.0 might break. In particular, after the message format is set to 0.10.0, one should not change it back to an earlier format as it may break consumers on versions before 0.10.0.0. 

##### potential breaking changes in 0.10.0.0

  * Starting from Kafka 0.10.0.0, the message format version in Kafka is represented as the Kafka version. For example, message format 0.9.0 refers to the highest message version supported by Kafka 0.9.0. 
  * Message format 0.10.0 has been introduced and it is used by default. It includes a timestamp field in the messages and relative offsets are used for compressed messages. 
  * ProduceRequest/Response v2 has been introduced and it is used by default to support message format 0.10.0 
  * FetchRequest/Response v2 has been introduced and it is used by default to support message format 0.10.0 
  * MessageFormatter interface was changed from `def writeTo(key: Array[Byte], value: Array[Byte], output: PrintStream)` to `def writeTo(consumerRecord: ConsumerRecord[Array[Byte], Array[Byte]], output: PrintStream)`
  * MessageReader interface was changed from `def readMessage(): KeyedMessage[Array[Byte], Array[Byte]]` to `def readMessage(): ProducerRecord[Array[Byte], Array[Byte]]`
  * MessageFormatter's package was changed from `kafka.tools` to `kafka.common`
  * MessageReader's package was changed from `kafka.tools` to `kafka.common`
  * MirrorMakerMessageHandler no longer exposes the `handle(record: MessageAndMetadata[Array[Byte], Array[Byte]])` method as it was never called. 

#### Upgrading from 0.8.0, 0.8.1.X or 0.8.2.X to 0.9.0.0

0.9.0.0 has potential breaking changes (please review before upgrading) and an
inter-broker protocol change from previous versions. This means that upgraded
brokers and clients may not be compatible with older versions. It is important
that you upgrade your Kafka cluster before upgrading your clients. If you are
using MirrorMaker downstream clusters should be upgraded first as well.

**For a rolling upgrade:**

  1. Update server.properties file on all brokers and add the following property: inter.broker.protocol.version=0.8.2.X 
  2. Upgrade the brokers. This can be done a broker at a time by simply bringing it down, updating the code, and restarting it. 
  3. Once the entire cluster is upgraded, bump the protocol version by editing inter.broker.protocol.version and setting it to 0.9.0.0.
  4. Restart the brokers one by one for the new protocol version to take effect 

**Note:** If you are willing to accept downtime, you can simply take all the brokers down, update the code and start all of them. They will start with the new protocol by default. 

**Note:** Bumping the protocol version and restarting can be done any time after the brokers were upgraded. It does not have to be immediately after. 

##### Potential breaking changes in 0.9.0.0

  * Java 1.6 is no longer supported. 
  * Scala 2.9 is no longer supported. 
  * Broker IDs above 1000 are now reserved by default to automatically assigned broker IDs. If your cluster has existing broker IDs above that threshold make sure to increase the reserved.broker.max.id broker configuration property accordingly. 
  * Configuration parameter replica.lag.max.messages was removed. Partition leaders will no longer consider the number of lagging messages when deciding which replicas are in sync. 
  * Configuration parameter replica.lag.time.max.ms now refers not just to the time passed since last fetch request from replica, but also to time since the replica last caught up. Replicas that are still fetching messages from leaders but did not catch up to the latest messages in replica.lag.time.max.ms will be considered out of sync. 
  * Compacted topics no longer accept messages without key and an exception is thrown by the producer if this is attempted. In 0.8.x, a message without key would cause the log compaction thread to subsequently complain and quit (and stop compacting all compacted topics). 
  * MirrorMaker no longer supports multiple target clusters. As a result it will only accept a single --consumer.config parameter. To mirror multiple source clusters, you will need at least one MirrorMaker instance per source cluster, each with its own consumer configuration. 
  * Tools packaged under _org.apache.kafka.clients.tools.*_ have been moved to _org.apache.kafka.tools.*_. All included scripts will still function as usual, only custom code directly importing these classes will be affected. 
  * The default Kafka JVM performance options (KAFKA_JVM_PERFORMANCE_OPTS) have been changed in kafka-run-class.sh. 
  * The kafka-topics.sh script (kafka.admin.TopicCommand) now exits with non-zero exit code on failure. 
  * The kafka-topics.sh script (kafka.admin.TopicCommand) will now print a warning when topic names risk metric collisions due to the use of a '.' or '_' in the topic name, and error in the case of an actual collision. 
  * The kafka-console-producer.sh script (kafka.tools.ConsoleProducer) will use the new producer instead of the old producer be default, and users have to specify 'old-producer' to use the old producer. 
  * By default all command line tools will print all logging messages to stderr instead of stdout. 

##### Notable changes in 0.9.0.1

  * The new broker id generation feature can be disabled by setting broker.id.generation.enable to false. 
  * Configuration parameter log.cleaner.enable is now true by default. This means topics with a cleanup.policy=compact will now be compacted by default, and 128 MB of heap will be allocated to the cleaner process via log.cleaner.dedupe.buffer.size. You may want to review log.cleaner.dedupe.buffer.size and the other log.cleaner configuration values based on your usage of compacted topics. 
  * Default value of configuration parameter fetch.min.bytes for the new consumer is now 1 by default. 

##### Deprecations in 0.9.0.0

  * Altering topic configuration from the kafka-topics.sh script (kafka.admin.TopicCommand) has been deprecated. Going forward, please use the kafka-configs.sh script (kafka.admin.ConfigCommand) for this functionality. 
  * The kafka-consumer-offset-checker.sh (kafka.tools.ConsumerOffsetChecker) has been deprecated. Going forward, please use kafka-consumer-groups.sh (kafka.admin.ConsumerGroupCommand) for this functionality. 
  * The kafka.tools.ProducerPerformance class has been deprecated. Going forward, please use org.apache.kafka.tools.ProducerPerformance for this functionality (kafka-producer-perf-test.sh will also be changed to use the new class). 

#### Upgrading from 0.8.1 to 0.8.2

0.8.2 is fully compatible with 0.8.1. The upgrade can be done one broker at a
time by simply bringing it down, updating the code, and restarting it.

#### Upgrading from 0.8.0 to 0.8.1

0.8.1 is fully compatible with 0.8. The upgrade can be done one broker at a
time by simply bringing it down, updating the code, and restarting it.

#### Upgrading from 0.7

Release 0.7 is incompatible with newer releases. Major changes were made to
the API, ZooKeeper data structures, and protocol, and configuration in order
to add replication (Which was missing in 0.7). The upgrade from 0.7 to later
versions requires a [special tool](https://cwiki.apache.org/confluence/display
/KAFKA/Migrating+from+0.7+to+0.8) for migration. This migration can be done
without downtime.

