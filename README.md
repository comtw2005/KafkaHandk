


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



### SSL 連線

Step 1 : 憑證產製


Kafka TLS/SSL Example
Part 1: Create the Root CA.
Part 2: Configure Vertica for Mutual Authentication.
Part 3: Configure Kafka.
Part 4: Loading Data Directly From Kafka.
Part 5: Configure the Scheduler.

= = = = = = = = = = = = = = = = 
Part 1: Create the Root CA
= = = = = = = = = = = = = = = = 
1. Generate a private key named root.key. 
   $ openssl genrsa -out root.key

2.Generating a self-signed root CA named root.crt.
   $ openssl req -new -x509 -key root.key -out root.crt


= = = = = = = = = = = = = = = = 
Part 2: Configure Vertica for Mutual Authentication
= = = = = = = = = = = = = = = = 
Step 1: Create Server Key and Certificate
   $ openssl genrsa -out server.key

   $ openssl req -new -key server.key -out server_reqout.txt

Sign the certificate request with the root CA:
   $ openssl x509 -req -in server_reqout.txt -days 3650 -sha256 -CAcreateserial -CA root.crt -CAkey root.key -out server.crt


= = = = = = = = = = = = = = = = 
Part 3: Configure Kafka
= = = = = = = = = = = = = = = =
Step 1: Create the Truststore and Keystore 
	$ keytool -keystore kafka.truststore.jks -alias CARoot -import -file root.crt

	$ keytool -keystore kafka01.keystore.jks -alias localhost -validity 365 -genkey -keyalg RSA -ext SAN=DNS:172.20.10.6

	$ keytool -keystore kafka01.keystore.jks -alias localhost -certreq -file kafka01.unsigned.crt


	$ openssl x509 -req -CA root.crt -CAkey root.key -in kafka01.unsigned.crt -out kafka01.signed.crt -days 365 -CAcreateserial
			 
	$ keytool -keystore kafka01.keystore.jks -alias CARoot -import -file root.crt

	$ keytool -keystore kafka01.keystore.jks -alias localhost  -import -file kafka01.signed.crt
			   
	$ scp kafka.truststore.jks kafka01.keystore.jks root@kafka01.mycompany.com:

Step 2: Letting Kafka Read the Keystore and Truststore Files

	~# cd /opt/kafka/config/
	/opt/kafka/config# cp /root/kafka01.keystore.jks /root/kafka.truststore.jks .

Step 3: Edit the Kafka Configuration to Use TLS/SSL Encryption(不一定要做)
	su -s /bin/bash kafka


#### 調整設定檔

```
listeners=SSL://:9093
#listeners=PLAINTEXT://:9092,SSL://:9093

# Listener name, hostname and port the broker will advertise to clients.
# If not set, it uses the value for "listeners".
#advertised.listeners=PLAINTEXT://your.host.name:9092
#advertised.listeners=PLAINTEXT://172.20.10.6:9092,PLAINTEXT2://172.20.10.6:9091
#advertised.listeners=PLAINTEXT://172.20.10.6:9092,SSL://172.20.10.6:9093
advertised.listeners=SSL://172.20.10.6:9093

```
listeners 依照需求看是不是要保留9092連線




```
ssl.keystore.location=/home/macro/Certificate/kafka01.keystore.jks
ssl.keystore.password=vertica
ssl.key.password=111111
ssl.truststore.location=//home/macro/Certificate/kafka.truststore.jks
ssl.truststore.password=111111
ssl.enabled.protocols=TLSv1.2,TLSv1.1,TLSv1
ssl.client.auth=required
```
SSL加密通訊（SSL）。
- `ssl.keystore.location` 和 `ssl.keystore.password`：指定使用的keystore的位置和密碼。
- `ssl.truststore.location` 和 `ssl.truststore.password`：指定使用的truststore的位置和密碼。
- `ssl.protocol`：指定使用的SSL協議版本。
- `ssl.client.auth`：啟用或禁用客戶端驗證。



####
ls -rlt /home/macro/Certificate/
```
-rw------- 1 macro macro 1679  八  20 00:23 root.key
-rw-rw-r-- 1 macro macro 1395  八  20 00:24 root.crt
-rw------- 1 macro macro 1675  八  20 00:24 server.key
-rw-rw-r-- 1 macro macro 1094  八  20 00:25 server_reqout.txt
-rw-rw-r-- 1 macro macro 1273  八  20 00:29 server.crt
-rw-rw-r-- 1 macro macro 1366  八  20 00:37 kafka.truststore.jks
-rw-rw-r-- 1 macro macro 1051  八  20 00:38 kafka01.unsigned.crt
-rw-rw-r-- 1 macro macro   41  八  20 00:38 root.srl
-rw-rw-r-- 1 macro macro 1208  八  20 00:38 kafka01.signed.crt
-rw-rw-r-- 1 macro macro 5054  八  20 00:39 kafka01.keystore.jks
```

Step 4: Restart Your Kafka Cluster
Step 5: Testing Your Configuration
	# openssl s_client -debug -connect kafka01.mycompany.com:9093 -tls1


= = = = = = = = = = = = = = = = 
Kafka TLS/SSL Example Part 4: Loading Data Directly From Kafka (我沒做)

= = = = = = = = = = = = = = = = 
$ export KVERTICA_PASS=server_key_password
$ vsql

= = = = = = = = = = = = = = = = 
Kafka TLS-SSL Example Part 5: Configure the Scheduler (我沒做)
= = = = = = = = = = = = = = = = 
$ keytool -keystore scheduler.truststore.jks -alias CARoot -import -file root.crt


keytool -keystore scheduler.keystore.jks -alias vsched -validity 365 -genkey -keyalg RSA  -dname CN=*.mycompany.com


= = = Client Filebeat

#生成客户端csr证书请求文件
openssl req -newkey rsa:2048 -days 1000 -nodes -keyout client.key -subj /CN=c172.20.10.6/OU=Development\ group/O=GG\ SIA/DC=GG/DC=com > client.csr
#使用ca证书与私钥签发客户端证书
openssl x509 -req -in client.csr -days 1000 -CA root.crt -CAkey root.key -set_serial 01 > client.crt
 
filebeat.yml

```
output.kafka:
   enabled: true
   hosts: ["172.20.10.6:9093"]
   topic: "test1"
   ssl.enabled: true
# Optional SSL. By default is off.
# List of root certificates for HTTPS server verifications
   ssl.certificate_authorities: ["/home/macro/Certificate/root.crt"]

# Certificate for SSL client authentication
   ssl.certificate: "/home/macro/Certificate/client.crt"

# Client Certificate Key
   ssl.key: "/home/macro/Certificate/client.key"

```





