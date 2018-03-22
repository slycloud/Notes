#### TroubleShoot

Job FatJar package
<hr>
在打包FatJar时需要注意，META-INF/services目录下的`*ClientProtocolProvider.*`文件里，是否包含"LocalClientProtocolProvider"与"YarnClientProtocolProvider"。
普通的打包结果，"Local*"和"Yarn*"一方可能被覆盖，而不是merge（使用如gradle fatjar plugin）。如果Yarn被覆盖，那么只能在Local mode下启动MapReduce job，而不是在Yarn mode下执行。
- YarnClientProtocolProvider is declared in hadoop-mapreduce-client-jobclient.jar
- LocalClientProtocolProvider is declared in hadoop-mapreduce-client-common.jar
