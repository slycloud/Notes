## Basic
Kafka具有一定的灵活性，即可以用于对数据可靠性要求高的应用，也可以用于对throughput要求高的应用，但是前提是进行正确的配置。正确的配置包括，cluster本身的配置（broker）以及application的配置（producer/consumer）。所以需要了解kafka的replication等机制以及broker和producer/consumer的各种配置的含义。

### Kafka Gaurantee

1. 同一个partition中的message有序
2. message被写入所有的in-sync replicas之后被认为committed（不一定是flush disk之后），而producer可以接收message的acknowledge, 接收的acknowledge的时机可以选择：fully committed之后，message被写入leader之后，或者通过network发送完成之后
3. 只要有1个replica是alive状态，message一旦被committed，就不会丢失
4. consumer只能接收committed message

### Kafka Replication

**Some Concepts**
- topic是由多个partition组成
- 一个partition存在于single disk上，而且由多个replicas组成。produce和consume都发生在leader replicas上，其他follower replicas则保持跟leader的同步

**In-sync Replicas**
- leader replica 当然是in-sync replica
- follower replica**同时**满足以下条件时是in-sync
  1. 6秒（可自定义）以内发送过heartbeat到zk
  2. 10秒（可自定义）之内从leader上同步了message
  3. 10秒（可自定义）之内从leader上获取了最新的message（注意：因为还是10秒的延迟，所以in-sync follower replicas上的message还是有可能不是最新的）

**注意**

如果一个replica在ins-sync和out-of-sync之间迅速切换的话，通常说明config有问题。比如错误的GC配置。

### Broker Config

通常broker的config包含两种类型：cluster level config和topic level config。

#### Replication factor (broker/topic level)

默认是3。对于一般的应用，3是够用的。2的太不够稳定，因为如果一台broker重启，则整个cluster会处于unstable的状态。但是对于银行业务等，可能还需要更高的replication，比如5。

##### Rack Config

如果1个partition的replicas都在一个rack上，仍然有风险（如top-of-rack swtich misbehavella）。所以最好配置rack config。

#### Unclean leader election (broker level)

默认是true。

当leader down时，从in-sync follower replicas中选举新的leader。但是如果没有in-sync follower replicas呢？因为此参数默认是true，所以此时会从可用的但不是in-sync的replicas中选择，也就是unclean leader election。

使用unclean leader election会导致message的丢失，但是如果不允许unclean leader election，我们需要等待old leader恢复，可能需要很长时间（如果是硬件原因，可能需要好几个小时或者更久）。而在此期间，partition会处于offline的状态。

#### Minumum In-Sync Replicas

注意，即使只有一个in-sync replica, 只要提交成功，message仍然会被认为是committed。我们可以设置min in-sync replica，确保message至少被committed到多于1个的replicas中。

### Producer Config for reliability

正确使用producer的重点在于：使用正确的ack以及error handling。

#### Acknowledgement

1. `0`: 当message通过network传输后acknowledge，即使aprtition是offline的状态
2. `1`: 当message写入leader之后acknowledge
3. `all`:当message写入all in-sycn replicas之后，最慢。通过使用async mode producer以及send large batch可以适当提速

#### Producer retry config

retry包括producer自动的retry以及application的retry。

**auto retry (retriable)**

自动retry：如果你不想要丢失message，最好让producer尝试retry（retriable error）。Kafka的cross-DC replication tool默认会无限地尝试retry。

注意，retry可能导致duplication，比如网络问题导致producer没有收到ack，但实际已经成功commit，此时retry将会导致重复。
retry和error handling可以保证message被处理at leat once，在Kafka 0.10.0中还不能保证exactly once。解决办法是，application（consumer)方面自己通过别的方式（比如unique id）去重，或者application（consumer）的处理逻辑为idempotent时也没有问题。

**error handling**

对于不是retriable的错误，我们需要另外处理，如：message size error，auth error，或者发送message到broker之前的serialization error。

### Consumer Config for reliability

只有一个要点: commit message offset after processed。

1. `group.id`: 拥有同样group.id的consumer共同处理topic
2. `auto.offset.reset`: 决定当没有offset被commit时consumer的行为。比如，当consumer刚启动时，或者consumer请求了不存在的offsets时（chapter 4中有解释）
  - `earliest`: 不管是否有一个有效的offset，都从头开始处理partition。减少了loss，但是会导致大量的重复处理
  - `latest`: 从partition的末尾开始。减少了重复处理，但是会导致loss
3. `enable.auto.commit`: 是否需要自动提交offset，或者自己自动提交offset
4. `auto.commit.interval.ms`: 如果选择设置为true，配置此参数设置auto commit的间隔。default：5s。越小的间隔（提交越频繁）虽然会引入代价，但减少的可能产生的重复（commit之前处理中断的情况）

#### Explicitly committing offsets

**frequent offset commit introduce performance overhead**

It's a trade-off between performance and duplication number in crash.
committing的性能代价相当于produce with acks=all。

**rebalance**

but the bigger picture is that this usually involves committing offsets before partitions are revoked and cleaning any state you maintain when you are assigned new partitions.

**make consumer to retry**

Kafka不支持对单个message进行ack，所以如果处理message #30失败，而处理message #31成功，也不能commit 31，否则30将会被认识处理完成，而实际并没有被处理。重新处理#30 (retriable)的解决办法有两个：

1. 处理 #30出问题时，commit offset #29，然后将#30缓存起来再次处理。此时，可以利用`Consumer#pause`保证poll不会再返回其他数据，从而使retry处理更加容易
2. 处理 #30出问题时，将其写入另一个retry topic，让consumer同时处理original/retry topic，或者用另一个consumer group处理retry topic，类似于dead-letter-queue system

**handling long processing times**

某些版本的kafka consumer，不能停止polling超过几分钟（见chapter 4）。如果单个message的处理太耗时，来不及处理完所有message，你也必须继续poll从而使client能按时发送heartbeat到broker。（如果consumer一直在poll，就能保证heartbeat按时发送给broker，也就不会触发rebalancing）。解决办法是：讲processing交个其他的worker thread处理，同时pause consumer，则consmer将会继续poll（发送heartbeat），但是consumer并不会收到更多的message，等worker threads处理完成后，再恢复consumer就可以了。

**exactly-once delivery**

Kafka本身不提供exactly-once的保证，aconsumer可以通过其他办法实现（注意，只能保证kafka committed message被写入其他数据库时，只有一份写入的结果，不包括在message被produce到Kafka时出现的duplication）。

- 一个方法是通过其他的数据库来去重，如果遇到重复的数据，则覆盖或者不处理，这个方法也叫做idempotent writes，是很常用的做法

- 另一个方法可用于使用支持事务的系统时，比如关系型数据库，或者用用atomic rename功能的HDFS。方法就是，讲records和offset写入同一个事务中，启动的时候，获取到latest records附带的offset，然后从该offset处开始处理（`Consumer#seek`）

### Validate System Reliability

#### 1. Validate config

分别测试broker和client的config很简单，也推荐这样做。

Class `VerifiableProducer` and `VerifiableConsumer`可以帮助你测试kafka(broker)是否征程运行。例如该producer可以产生一系列message，还可以设置acks，retries，produce rate等。而该producer则负责complementary check。

#### 2. Validate applications

检查error handling code, offset commit, rebalance listener等等。

建议在以下failure condition下测试：

- client lose connectivity to the server
- leader election
- rolling restart of brokers
- rolling restart of consumers
- rolling restart of consumers
- rolling restart of producers

#### 3. Monitor reliability in production

Kafka's java clients include JMX metrics for monitoring client-side status and events.

**Producer**

- 检测reliability的2个重要的metrics是**error-rate**和**retry-rate per record（aggregated）**
- monitor WARN logs which say something along the lines of `Got error produce response with correlation id 5689 on topic-partition [topic-1,3], retrying (two attempts left). Error: …`. If you see events with 0 attempts left, the producer is running out of retries.

**Consumer**

- 最重要的metrics是**consumer lag**。因为一次poll会获取多个message，处理完这些message需要一定时间，所以通常会有一定的lag，设置监测lag的alert不是一件容易的事，可以使用LinkedIn的工具Burrow

**reasonable process time**

为了确认message在一定时间内被处理，Kafka在0.10.0之后，在所有message中包含了一个timestamp表示message被produce的时间，方便track。

**end-to-end monitoring**

producer记录每秒产生的message数，consumer记录每秒consume的message数以及event从被produce到被consume的时间差（lag），并通过一个系统来合并这些metrics，确保没有message lose以及message能在一定时间内被处理完
