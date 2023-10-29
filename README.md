第 1-5 天：基本概念

    天 1-2：瞭解 Kafka 的基本概念和架構。
    天 3-5：學習 Kafka 生產者和消費者的基本使用，如如何創建主題、發送和讀取消息。

第 6-10 天：深入主題

    天 6-7：深入研究 Kafka 主題，學習如何管理主題、分區和副本。
    天 8-10：學習 Kafka 位移的概念，如何管理位移，並探討位移的自動提交和手動提交。

第 11-15 天：進階主題

    天 11-12：研究 Kafka 分區的更多細節，如何選擇適當的分區數量。
    天 13-15：深入理解 Kafka 副本，配置和實現高可用性。

第 16-20 天：進階主題

    天 16-17：學習 Kafka Streams，開始建立實時數據處理應用。
    天 18-20：瞭解 Kafka Connect，探討如何連接外部數據源和目的地。

第 21-25 天：Kafka 安全性和監控

    天 21-22：深入研究 Kafka 安全性，學習如何設置身份驗證、授權和加密。
    天 23-25：探討 Kafka 監控和運維，學習如何設置監控和進行故障排除。

第 26-30 天：實踐和項目

    天 26-28：開始構建自己的 Kafka 項目，包括生產者、消費者或流處理應用。
    天 29-30：回顧學習，解決任何未解決的問題，並確保您對 Kafka 的理解和應用有信心。

這只是一個示例學習計畫，您可以根據自己的需求和學習進度進行調整。此外，重要的是實際進行項目工作，以鞏固所學知識。通過持之以恆的學習和實踐，您可以在 30 天內建立堅實的 Kafka 技能。


























## Kafka 从何而来？
Kafka 最初是 LinkedIn 的一个内部基础设施系统。
虽然有很多数据库和系统可以用来存储数据，但刚好缺一个可以帮助处理持续数据流的组件。

Kafka 是一个流平台：在这个平台上可以发布和订阅数据流，并把它们保存起来、进行处理，这就是构建 Kafka 的初衷。


Kafka 有点像消息系统，允许发布和订阅消息流。它类似于 MQ，但仍然存在很多重要的不同点
1. 作为一分布式系统，Kafka 以集群的方式运行，可以自由伸缩
2. Kafka 可以按照你的要求存储数据，保存多久都可以
3. 消息系统只会传递消息，而 Kafka 的流式处理能力让你只用很少的代码就能够动态地处理派生流和数据集。

Hadoop 可以存储和定期处理大量的数据文件，而 Kafka 可以存储和持续处理大型的数据流。
最大不同体现在持续的低延迟处理和批处理之间的差异。
Hadoop 和大数据主要应用在数据分析上，而 Kafka 因其低延迟的特点更适合用在核心的业务应用上。






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


## kafka-broker-api-versions.sh
```
./kafka-broker-api-versions.sh --bootstrap-server 172.20.10.6:9092
== > 
macro-VirtualBox:9093 (id: 2 rack: null) -> (
	Produce(0): 0 to 9 [usable: 9],
	Fetch(1): 0 to 13 [usable: 13],
	ListOffsets(2): 0 to 7 [usable: 7],
	Metadata(3): 0 to 12 [usable: 12],
	LeaderAndIsr(4): 0 to 6 [usable: 6],
	StopReplica(5): 0 to 3 [usable: 3],
	UpdateMetadata(6): 0 to 7 [usable: 7],
	ControlledShutdown(7): 0 to 3 [usable: 3],
	OffsetCommit(8): 0 to 8 [usable: 8],
	OffsetFetch(9): 0 to 8 [usable: 8],
	FindCoordinator(10): 0 to 4 [usable: 4],
	JoinGroup(11): 0 to 9 [usable: 9],
	Heartbeat(12): 0 to 4 [usable: 4],
	LeaveGroup(13): 0 to 5 [usable: 5],
	SyncGroup(14): 0 to 5 [usable: 5],
	DescribeGroups(15): 0 to 5 [usable: 5],
	ListGroups(16): 0 to 4 [usable: 4],
	SaslHandshake(17): 0 to 1 [usable: 1],
	ApiVersions(18): 0 to 3 [usable: 3],
	CreateTopics(19): 0 to 7 [usable: 7],
	DeleteTopics(20): 0 to 6 [usable: 6],
	DeleteRecords(21): 0 to 2 [usable: 2],
	InitProducerId(22): 0 to 4 [usable: 4],
	OffsetForLeaderEpoch(23): 0 to 4 [usable: 4],
	AddPartitionsToTxn(24): 0 to 3 [usable: 3],
	AddOffsetsToTxn(25): 0 to 3 [usable: 3],
	EndTxn(26): 0 to 3 [usable: 3],
	WriteTxnMarkers(27): 0 to 1 [usable: 1],
	TxnOffsetCommit(28): 0 to 3 [usable: 3],
	DescribeAcls(29): 0 to 3 [usable: 3],
	CreateAcls(30): 0 to 3 [usable: 3],
	DeleteAcls(31): 0 to 3 [usable: 3],
	DescribeConfigs(32): 0 to 4 [usable: 4],
	AlterConfigs(33): 0 to 2 [usable: 2],
	AlterReplicaLogDirs(34): 0 to 2 [usable: 2],
	DescribeLogDirs(35): 0 to 4 [usable: 4],
	SaslAuthenticate(36): 0 to 2 [usable: 2],
	CreatePartitions(37): 0 to 3 [usable: 3],
	CreateDelegationToken(38): 0 to 3 [usable: 3],
	RenewDelegationToken(39): 0 to 2 [usable: 2],
	ExpireDelegationToken(40): 0 to 2 [usable: 2],
	DescribeDelegationToken(41): 0 to 3 [usable: 3],
	DeleteGroups(42): 0 to 2 [usable: 2],
	ElectLeaders(43): 0 to 2 [usable: 2],
	IncrementalAlterConfigs(44): 0 to 1 [usable: 1],
	AlterPartitionReassignments(45): 0 [usable: 0],
	ListPartitionReassignments(46): 0 [usable: 0],
	OffsetDelete(47): 0 [usable: 0],
	DescribeClientQuotas(48): 0 to 1 [usable: 1],
	AlterClientQuotas(49): 0 to 1 [usable: 1],
	DescribeUserScramCredentials(50): 0 [usable: 0],
	AlterUserScramCredentials(51): 0 [usable: 0],
	AlterPartition(56): 0 to 2 [usable: 2],
	UpdateFeatures(57): 0 to 1 [usable: 1],
	DescribeCluster(60): 0 [usable: 0],
	DescribeProducers(61): 0 [usable: 0],
	DescribeTransactions(65): 0 [usable: 0],
	ListTransactions(66): 0 [usable: 0],
	AllocateProducerIds(67): 0 [usable: 0]
)
macro-VirtualBox:9091 (id: 1 rack: null) -> (
	Produce(0): 0 to 9 [usable: 9],
	Fetch(1): 0 to 13 [usable: 13],
	ListOffsets(2): 0 to 7 [usable: 7],
```

## ./bin/zookeeper-shell.sh  172.20.10.6:2181
列出主題列表的。如果您想要列出目前正在運行的 Kafka Broker，您可以使用 Zookeeper 的命令列工具來查詢。
./bin/zookeeper-shell.sh  172.20.10.6:2181
== >
``` 
Connecting to 172.20.10.6:2181
Welcome to ZooKeeper!
JLine support is disabled

WATCHER::

WatchedEvent state:SyncConnected type:None path:null
```
列出註冊在 Zookeeper 中的 Broker ID
```
ls /brokers/ids
[0, 1, 2]

```
逐一查詢這些 Broker ID 來獲取更多有關每個 Broker 的詳細信息。使用以下命令，將 BROKER_ID 替換為實際的 Broker ID：
get /brokers/ids/0

```
{"features":{},"listener_security_protocol_map":{"PLAINTEXT":"PLAINTEXT"},"endpoints":["PLAINTEXT://macro-VirtualBox:9092"],"jmx_port":-1,"port":9092,"host":"macro-VirtualBox","version":5,"timestamp":"1692025259506"}
```






