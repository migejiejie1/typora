```shell

================================ kafka-1 =====================================
server.properties
############ Server ############
broker.id=1                    //集群中id需要修改
log.dirs=/data/kafka_logs
listeners=PLAINTEXT://:19092
advertised.listeners=PLAINTEXT://10.160.58.31:19092   //监听地址需要修改

num.network.threads=3
num.io.threads=8
background.threads=10
queued.max.requests=500

message.max.bytes=10485760
replica.fetch.max.bytes=10485760
fetch.message.max.bytes=10485760


############ Socket ############
socket.send.buffer.bytes=8388608
socket.receive.buffer.bytes=8388608
socket.request.max.bytes=104857600


############ Topic ############
auto.create.topics.enable=false
delete.topic.enable=true
num.partitions=5
default.replication.factor=2


############ Leader、replicas、offset ############
replica.lag.max.messages =4000
replica.socket.timeout.ms=30000
replica.fetch.min.bytes=1
replica.fetch.wait.max.ms=500
num.replica.fetchers=1
replica.high.watermark.checkpoint.interval.ms=5000
replica.lag.time.max.ms=10000
replica.socket.receive.buffer.bytes=65536
controlled.shutdown.enable=false
controlled.shutdown.max.retries=3
controlled.shutdown.retry.backoff.ms=5000
controller.socket.timeout.ms=30000
controller.message.queue.size=10
leader.imbalance.check.interval.seconds=300
leader.imbalance.per.broker.percentage=10


offset.metadata.max.bytes=4096
offsets.commit.required.acks=-1
offsets.commit.timeout.ms=5000
offsets.load.buffer.size=5242880
offsets.retention.check.interval.ms=600000
offsets.retention.minutes=1440
offsets.topic.compression.codec=0
offsets.topic.num.partitions=50
offsets.topic.replication.factor=3
offsets.topic.segment.bytes=104857600


############ Log ############
log.cleanup.policy=delete
log.retention.hours=72
log.cleanup.interval.mins=10
log.retention.bytes=-1
log.retention.check.interval.ms=300000
log.segment.bytes=1073741824
log.roll.hours=72

log.cleaner.enable=false
log.cleaner.threads=1
log.cleaner.io.max.bytes.per.second=1.7976931348623157E308
log.cleaner.dedupe.buffer.size=524288000
log.cleaner.io.buffer.size=524288
log.cleaner.io.buffer.load.factor=0.9
log.cleaner.backoff.ms=15000
log.cleaner.min.cleanable.ratio=0.5
log.cleaner.delete.retention.ms=86400000
log.index.size.max.bytes=10485760

#log.flush.interval.messages=9223372036854775807
#log.flush.scheduler.interval.ms=9223372036854775807
#log.flush.interval.ms=9223372036854775807

#log.message.timestamp.type=CreateTime
#log.message.timestamp.difference.max.ms=9223372036854775807


############ Zookeeper ############
zookeeper.connect=10.160.58.41:12181,10.160.58.42:12181,10.160.58.43:12181   //根据Zookeeper集群IP进行修改
zookeeper.connection.timeout.ms=6000
zookeeper.session.timeout.ms=6000
zookeeper.sync.time.ms =2000


############ consumer、producer ############
fetch.min.bytes=10
fetch.wait.max.ms=100

acks=1
buffer.memory=67108864
batch.size=64000
linger.ms=100

```

