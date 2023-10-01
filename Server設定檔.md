```
############################# Log Retention Policy #############################

# The following configurations control the disposal of log segments. The policy can
# be set to delete segments after a period of time, or after a given size has accumulated.
# A segment will be deleted whenever *either* of these criteria are met. Deletion always happens
# from the end of the log.

# The minimum age of a log file to be eligible for deletion due to age
#log.retention.hours=168
#log.retention.hours=1
log.retention.minutes=1

```


今年要來試試看 Log Retention Policy

Step 1: 啟動zookeper, server
Step 2: 創建topic
```
 ~/kafka_2.13-3.3.1/bin/kafka-topics.sh --create --bootstrap-server 172.20.10.6:9092 --topic test1
```
Step 3: 確認查詢狀態
```
/home/macro/kafka_2.13-3.3.1/bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 172.20.10.6:9092 --topic test1
/home/macro/kafka_2.13-3.3.1/bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 172.20.10.6:9092 --topic test1 --time 1
/home/macro/kafka_2.13-3.3.1/bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 172.20.10.6:9092 --topic test1 --time 2
```
![圖片](https://github.com/comtw2005/KafkaHandle/assets/46416652/8e523bf0-c7d3-45c7-af0c-b4f68c3795f7)

Step 4: Producer
```
./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test1

macro@macro-VirtualBox:~/kafka_2.13-3.3.1$ ./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test1
>AAAAAAAAAAAAAA
>BBBBBBBBBBBBBB
>
```

Step 5:確認查詢狀態
```
macro@macro-VirtualBox:~/kafka_2.13-3.3.1$ /home/macro/kafka_2.13-3.3.1/bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 172.20.10.6:9092 --topic test1 
test1:0:2
macro@macro-VirtualBox:~/kafka_2.13-3.3.1$ /home/macro/kafka_2.13-3.3.1/bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 172.20.10.6:9092 --topic test1 --time 1
test1:0:0
macro@macro-VirtualBox:~/kafka_2.13-3.3.1$ /home/macro/kafka_2.13-3.3.1/bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 172.20.10.6:9092 --topic test1 --time 2
test1:0:0
```
Step 6: 等待一分鐘
```
macro@macro-VirtualBox:~/kafka_2.13-3.3.1$ /home/macro/kafka_2.13-3.3.1/bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 172.20.10.6:9092 --topic test1 
test1:0:2
macro@macro-VirtualBox:~/kafka_2.13-3.3.1$ /home/macro/kafka_2.13-3.3.1/bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 172.20.10.6:9092 --topic test1 --time 1
macro@macro-VirtualBox:~/kafka_2.13-3.3.1$ /home/macro/kafka_2.13-3.3.1/bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 172.20.10.6:9092 --topic test1 --time 2
macro@macro-VirtualBox:~/kafka_2.13-3.3.1$ 
```
同步看到server.log
```
[2023-10-01 08:59:48,019] INFO [LocalLog partition=test1-0, dir=/tmp/kafka-log] Rolled new log segment at offset 2 in 12 ms. (kafka.log.LocalLog)
[2023-10-01 08:59:48,032] INFO [ProducerStateManager partition=test1-0] Wrote producer snapshot at offset 2 with 1 producer ids in 8 ms. (kafka.log.ProducerStateManager)
[2023-10-01 08:59:48,036] INFO [UnifiedLog partition=test1-0, dir=/tmp/kafka-log] Deleting segment LogSegment(baseOffset=0, size=164, lastModifiedTime=1696121898177, largestRecordTimestamp=Some(1696121897169)) due to retention time 60000ms breach based on the largest record timestamp in the segment (kafka.log.UnifiedLog)
[2023-10-01 08:59:48,044] INFO [UnifiedLog partition=test1-0, dir=/tmp/kafka-log] Incremented log start offset to 2 due to segment deletion (kafka.log.UnifiedLog)

[2023-10-01 09:00:48,047] INFO [LocalLog partition=test1-0, dir=/tmp/kafka-log] Deleting segment files LogSegment(baseOffset=0, size=164, lastModifiedTime=1696121898177, largestRecordTimestamp=Some(1696121897169)) (kafka.log.LocalLog$)
[2023-10-01 09:00:48,063] INFO Deleted log /tmp/kafka-log/test1-0/00000000000000000000.log.deleted. (kafka.log.LogSegment)
[2023-10-01 09:00:48,084] INFO Deleted offset index /tmp/kafka-log/test1-0/00000000000000000000.index.deleted. (kafka.log.LogSegment)
[2023-10-01 09:00:48,086] INFO Deleted time index /tmp/kafka-log/test1-0/00000000000000000000.timeindex.deleted. (kafka.log.LogSegment)
```
Step 7: Producer
```
macro@macro-VirtualBox:~/kafka_2.13-3.3.1$ ./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test1
>CCCCCCCCCCCCCC
>DDDDDDDDDDDDD
>EEEEEEEEEEEEE
>
```
```
macro@macro-VirtualBox:~/kafka_2.13-3.3.1$ /home/macro/kafka_2.13-3.3.1/bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 172.20.10.6:9092 --topic test1 
test1:0:5
macro@macro-VirtualBox:~/kafka_2.13-3.3.1$ /home/macro/kafka_2.13-3.3.1/bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 172.20.10.6:9092 --topic test1 --time 1
test1:0:2
macro@macro-VirtualBox:~/kafka_2.13-3.3.1$ /home/macro/kafka_2.13-3.3.1/bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 172.20.10.6:9092 --topic test1 --time 2
test1:0:2
```



















