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

### Producer Config

正确使用producer的重点在于：使用正确的ack以及error handling。

#### Acknowledgement

1. `0`: 当message通过network传输后acknowledge，即使aprtition是offline的状态
2. `1`: 当message写入leader之后acknowledge
3. `all`:当message写入all in-sycn replicas之后，最慢。通过使用async mode producer以及send large batch可以适当提速

#### Producer retry

retry报货producer自动的retry以及application的retry。注意，retry可能导致duplication，比如网络问题导致producer没有收到ack，但实际已经成功commit，此时retry将会导致重复。解决办法是，application（consumer)方面自己通过别的方式（比如unique id）去重，或者application（consumer）的处理逻辑为idempotent时也没有问题。
