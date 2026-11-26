# Kafka CLI Commands Cheat Sheet

A quick reference for the Kafka commands used in the
`Kafka-Microservices-Learning` project.

## 1. The common command structure

When Kafka runs inside Docker, the general pattern is:

``` powershell
docker exec kafka-1 /opt/kafka/bin/<kafka-script>.sh --bootstrap-server localhost:9092 <arguments>
```

  -------------------------------------------------------------------------
  Part                                  Meaning
  ------------------------------------- -----------------------------------
  `docker exec`                         Run a command inside a running
                                        container

  `kafka-1`                             Name of our Kafka container

  `/opt/kafka/bin/`                     Folder containing Kafka
                                        command-line tools

  `<kafka-script>.sh`                   The Kafka operation to run

  `--bootstrap-server localhost:9092`   Address of the Kafka broker to
                                        connect to
  -------------------------------------------------------------------------

**Memory trick:** Docker enters the container → Kafka script selects the
operation → arguments tell Kafka what to do.

## 2. Check whether the broker is responding

``` powershell
docker exec kafka-1 /opt/kafka/bin/kafka-broker-api-versions.sh --bootstrap-server localhost:9092
```

**Purpose:** Checks whether a Kafka client can connect to the broker and
lists the APIs it supports.

Useful things to recognize in the output:

-   `localhost:9092` --- broker address
-   `id: 1` --- broker ID
-   `isFenced: false` --- broker is not fenced

## 3. Topic commands

### Create a topic

``` powershell
docker exec kafka-1 /opt/kafka/bin/kafka-topics.sh --bootstrap-server localhost:9092 --create --topic order-topic --partitions 3 --replication-factor 1
```

  Argument                   Meaning
  -------------------------- ------------------------------------
  `--create`                 Create a topic
  `--topic order-topic`      Name the topic `order-topic`
  `--partitions 3`           Create three partitions
  `--replication-factor 1`   Keep one replica of each partition

**Why replication factor 1?** Our current Kafka setup has one broker. A
replication factor greater than one requires multiple brokers.

### List topics

``` powershell
docker exec kafka-1 /opt/kafka/bin/kafka-topics.sh --bootstrap-server localhost:9092 --list
```

**Purpose:** Displays the topics on the Kafka cluster.

### Describe a topic

``` powershell
docker exec kafka-1 /opt/kafka/bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic order-topic
```

**Purpose:** Shows topic details such as partition count, leaders,
replicas, and in-sync replicas (ISR).

## 4. Console producer and consumer commands

These are handy for testing Kafka manually before connecting our Spring
Boot services.

### Start a console producer

``` powershell
docker exec -it kafka-1 /opt/kafka/bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic order-topic
```

Type a message and press Enter to publish it. Use `Ctrl+C` to exit.

### Start a console consumer

``` powershell
docker exec -it kafka-1 /opt/kafka/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic order-topic --from-beginning
```

**Purpose:** Reads records from the topic, including existing records
from the beginning.

Use `Ctrl+C` to exit.

## 5. Consumer group commands

We'll use these when we study consumer groups.

### List consumer groups

``` powershell
docker exec kafka-1 /opt/kafka/bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list
```

### Describe a consumer group

Replace `your-group-id` with the actual group ID:

``` powershell
docker exec kafka-1 /opt/kafka/bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group your-group-id
```

**Purpose:** Shows group members, partition assignments, current
offsets, log-end offsets, and lag.

## 6. Quick command memory table

  Script                           Remember it as
  -------------------------------- ----------------------------------
  `kafka-broker-api-versions.sh`   Check broker connection / APIs
  `kafka-topics.sh`                Create, list, and inspect topics
  `kafka-console-producer.sh`      Send test messages
  `kafka-console-consumer.sh`      Read test messages
  `kafka-consumer-groups.sh`       Inspect consumer groups

## 7. Practice: build the command from memory

Try to recall these without looking at the examples:

1.  Which Docker command runs a process inside `kafka-1`?
2.  Which option specifies the broker address?
3.  Which Kafka script manages topics?
4.  Which option creates a topic?
5.  Which options set the partition count and replication factor?

**Learning rule:** Don't try to memorize every character. Understand the
command pattern and practise using each command. We'll add new commands
here as we reach those Kafka concepts.
