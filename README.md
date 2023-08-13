


# 調整設定檔
```
1. Log Basics

# A comma separated list of directories under which to store log files
log.dirs=/tmp/kafka-logs

# The default number of log partitions per topic. More partitions allow greater
# parallelism for consumption, but this will also result in more files across
# the brokers.
num.partitions=4

這個就會影響到kafka partition數量
```

## kafka-topics.sh
```
1. 創建Topic
   ~/kafka_2.13-3.3.1/bin/kafka-topics.sh --create --bootstrap-server 127.0.0.1:9092 --topic ${topic name}
   ~/kafka_2.13-3.3.1/bin/kafka-topics.sh --create --bootstrap-server 127.0.0.1:9092 --topic test1
   ==> Created topic test1.

   建立副本
   --replication-factor 副本數為6
   ~/kafka_2.13-3.3.1/bin/kafka-topics.sh --create --bootstrap-server 127.0.0.1:9092 --replication-factor 2 --partitions 3 --topic testP
   ==> Created topic testP.

    --topic：指定主題名稱。
    --partitions：指定主題的分區數量。
    --replication-factor：指定每個分區的複製因子（副本數量）。
    --bootstrap-server：指定 Kafka 伺服器的連線位置，例如 localhost:9092。

3. 查看查看所有的Topic
   ~/kafka_2.13-3.3.1/bin/kafka-topics.sh --list --bootstrap-server localhost:9092
   ==> test1

5. 查看版本
   ~/kafka_2.13-3.3.1/bin/kafka-topics.sh --version
   ==> 3.3.1 (Commit:e23c59d00e687ff5)
  
7. 刪除 Topic
   ~/kafka_2.13-3.3.1/bin/kafka-topics.sh -delete --bootstrap-server localhost:9092 --topic test1

9. Topic 詳細描述
   kafka-topics.sh
   ~/kafka_2.13-3.3.1/bin/kafka-topics.sh --describe --bootstrap-server localhost:9092  --topic test1

==> Topic: test1	TopicId: aKGLhOFLSw2TJlcEaTKQOQ	PartitionCount: 4	ReplicationFactor: 1	Configs: 
   	Topic: test1	Partition: 0	Leader: 0	Replicas: 0	Isr: 0
	  Topic: test1	Partition: 1	Leader: 0	Replicas: 0	Isr: 0
	  Topic: test1	Partition: 2	Leader: 0	Replicas: 0	Isr: 0
	  Topic: test1	Partition: 3	Leader: 0	Replicas: 0	Isr: 0

    Partition: 0 => 代表該 Topic 編號0的partition
    Leader: 0 => 表示 partition 0的 leader 位於哪個 broker，因為只有一個 broker，所以就是在 Broker 0
    Replicas: 0 => 表示有幾個備份
    Isr: 1 => 表示在哪幾個 broker 可以找到這個 partition

==> 如果有備份
    Topic: testP	TopicId: 2IUT_zjzRQqKYQeuBVmhTA	PartitionCount: 3	ReplicationFactor: 2	Configs: 
	  Topic: testP	Partition: 0	Leader: 2	Replicas: 2,0	Isr: 2,0
	  Topic: testP	Partition: 1	Leader: 1	Replicas: 1,2	Isr: 1,2
	  Topic: testP	Partition: 2	Leader: 0	Replicas: 0,1	Isr: 0,1


==> 將副本放置於其他主機上
     ~/kafka_2.13-3.3.1/bin/kafka-topics.sh --create --bootstrap-server localhost:9091,localhost:9092,localhost:9093 --replication-factor 2 --partitions 4 --topic testP3
==> 確認topic 詳細資料, 同時確認連線 (有連線問題時), 會顯示 Broker may not be available , 因為9095跟9090是不存在的
    ~/kafka_2.13-3.3.1/bin/kafka-topics.sh --describe --topic testPt --bootstrap-server localhost:9095,localhost:9090,localhost:9093

[2023-08-14 06:51:45,731] WARN [AdminClient clientId=adminclient-1] Connection to node -1 (localhost/172.20.10.6:9095) could not be established. Broker may not be available. (org.apache.kafka.clients.NetworkClient)
[2023-08-14 06:51:45,742] WARN [AdminClient clientId=adminclient-1] Connection to node -2 (localhost/172.20.10.6:9090) could not be established. Broker may not be available. (org.apache.kafka.clients.NetworkClient)
Topic: testPt	TopicId: XG6Q51S2STK4dycU3G_PdQ	PartitionCount: 4	ReplicationFactor: 2	Configs: 
	Topic: testPt	Partition: 0	Leader: 2	Replicas: 2,0	Isr: 2,0
	Topic: testPt	Partition: 1	Leader: 1	Replicas: 1,2	Isr: 1,2
	Topic: testPt	Partition: 2	Leader: 0	Replicas: 0,1	Isr: 0,1
	Topic: testPt	Partition: 3	Leader: 2	Replicas: 2,1	Isr: 2,1




```

## kafka-run-class.sh
```
1. 查看Topic offset
   ~/kafka_2.13-3.3.1/bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list localhost:9092




## 本機模擬三台kafka
1. 設定檔 config/server.properties 在準備 config/server_1.properties、config/server_2.properties
   三個參數需要調整, 
  broker.id=2
  listeners=PLAINTEXT://localhost:9094
  log.dirs=/usr/local/var/lib/kafka-logs/broker2

  broker.id=3
  listeners=PLAINTEXT://localhost:9095
  log.dirs=/usr/local/var/lib/kafka-logs/broker3
```









