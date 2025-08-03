# Installation

Download kafka.
```shell
wget https://downloads.apache.org/kafka/3.8.0/kafka_2.13-3.8.0.tgz
```

Extract Kafka from the archive.
```shell
tar -xzf kafka_2.13-3.8.0.tgz
cd kafka_2.13-3.8.0
```

Generate a cluster UUID that will uniquely identify the Kafka cluster. It will be used by KRaft controller.
```shell
KAFKA_CLUSTER_ID="$(bin/kafka-storage.sh random-uuid)"
```

KRaft requires the log directories to be configured. Run the following command to configure the log directories passing the cluster ID.
```shell
bin/kafka-storage.sh format -t $KAFKA_CLUSTER_ID -c config/kraft/server.properties
```

## Start the Kafka server.

```shell
bin/kafka-server-start.sh config/kraft/server.properties
```

## Create topic and start producer.

Create topic "news" :
```shell
bin/kafka-topics.sh --create --topic news --bootstrap-server localhost:9092
```

We need a producer  to send messages to Kafka.
```shell
bin/kafka-console-producer.sh   --bootstrap-server localhost:9092   --topic news
```
Write somes messages.

In a new terminal, create a consumer to read the messages from Kafka
```shell
bin/kafka-console-consumer.sh   --bootstrap-server localhost:9092   --topic news   --from-beginning
```


You should see all the messages from producer :

```text
maishuji@maishuji:~/Workplace/ibm-data-engineer/ibm-kafka/kafka_2.13-3.8.0$ bin/kafka-console-consumer.sh   --bootstrap-server localhost:9092   --topic news   --from-beginning
Good morning
Good day
Enjoy the Kafka lab
```

## Exploring Kafka directories
```shell
cd /
ls
```
In the root directory; there is the folder `kraft-combine-logs` that contains all the logs.

```shell
ls /tmp/kraft-combined-logs/news-0
```

```text
maishuji@maishuji:/tmp$ ls /tmp/kraft-combined-logs/news-0
00000000000000000000.index      leader-epoch-checkpoint
00000000000000000000.log        partition.metadata
00000000000000000000.timeindex
```

All messages are stored in the ̀news-0`

## Clean up
̀`Ctrl+C`on producer, consumer, and kafka server terminal to stop the services.