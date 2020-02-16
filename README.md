# Kafka-Cluster
Setup multi broker kafka cluster on multiple machine.
You'll find a lot of resources on internet to setup a cluster with multiple brokers on single node, the apache-kafka quickstart is well documented for this purpose : https://kafka.apache.org/quickstart.

In my case i've choose to use three physical machine to simulate an on-premise company setup. All of them are under Ubuntu. 
Below the corresponding IP adress i'll use for the following of the demonstration : 
* 192.168.1.50
* 192.168.1.51
* 192.168.1.52

# 1-Manual installation 

## Download kafka 
First of all download kafka & copy it to all servers : 

```
wget https://www-eu.apache.org/dist/kafka/2.4.0/kafka_2.12-2.4.0.tgz
tar -xzf kafka_2.12-2.4.0.tgz

for host in 192.168.1.50 192.168.1.51 192.168.1.52; do
    scp somefile $host:~/kafka/
done
```


## Configure Kafka-brokers 
Edit config/server.properties file on each node :
```
cd kafka kafka_2.12-2.4.0
vim config/server.properties
```
There are two essentials points needed to be configure : 
* broker.id : each broker (kafka-server) must have a unique id
* zookeeper.connect: each zookeper-server must be informed

In my case : 
* broker.id = 1 (2 & 3 on others node)

```
zookeeper.connect=192.168.1.50:2181,192.168.1.51:2181, 192.158.1.52:2181
```
## Launch Zookeper server 
To do on each node.

```
bin/zookeeper-server-start.sh config/zookeeper.properties
```

## Launch Kafka server
To do on each node.

```
bin/kafka-server-start.sh config/server.properties
```

## Create a topic

```
bin/kafka-topics.sh --create --bootstrap-server 192.168.1.50:9092 --replication-factor 3 --partitions 3 --topic mytopic
```

## Send messages with Kafka producer

```
bin/kafka-console-producer.sh --broker-list 192.168.1.50:9092 --topic mytopic
```

## Start a consumer attach to mytopic
Open a second terminal & run 

```
bin/kafka-console-consumer.sh --bootstrap-server 192.168.1.50:9092 --topic mytopic
```

For both consumer & producer, you can pass a list of kafka-server in case the first one doesn't respond : 

```
bin/kafka-console-consumer.sh --bootstrap-server 192.168.1.50:9092 192.168.1.51:9092 192.168.1.52:9092--topic mytopic
```
NB: Only the first one will be used, except if an error occured


You can add the option ```--from-beginning``` to collect historical messages from mytopic

# 2-Deploy with Docker swarm
To do 
