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
