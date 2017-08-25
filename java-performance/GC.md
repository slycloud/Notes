# GC

## GC Algorithm

algorightm | Serial | Throughput | CMS | G1GC
----|------|----|----|----
thread num | 1 | N | N | N
Young GC | stop-the-world | stop-the-world | stop-the-world | stop-the-world
Old GC | <ul><li>stop-the-world</li> <li>full compaction (old generation)</li></ul> | same with **Serial** | <ul><li>partial stop-the-world (when scanning old generation concurrently)</li> <li>no compaction when scanning</li></ul> | <ul><li>least stop</li> <li>partial compaction (compare to CMS)</li></ul>
advantage | -| throughput | less pause | less pause
disadvantage | - | - | high CPU usage | high CPU usage
when to use | - | focus on average response time | enough CPU resouce; hope to reduce max response time | heap greater than 4G; enough CPU resouce

## GC Tuning

### Throughput Collector

**basic operations**

- collect young generation
- collect old generation

#### 1. Adaptive Sizing
JVM will resize heap(generations) to acheive the performance goal.

It's enough for most of the time, and recommend for a new hand.

**performance goal metric**

- MaxGCPauseMillis
    - be careful to set a reasonable value or not set. Unreasonable little value will cause a small heap, leading to frequency full GC and poor performance.
- XX:GCTimeRatio

#### 2. Static Sizing
let minHeapSize == maxHeapSize

### CMS Collector

### G1GC Collector

