
### 什么是JMX？
JMX主要为了监控JAVA应用的状态，包括内存使用量，GC情况，以及通过MBean自定义的监控数据（如，Redis等的访问频率和响应时间）。

JMX正常工作包含两部分：MBean Server + Connector Server(jmxmp Server or rmi Server)
- MBean Server：管理MBean
- Connector Server：供远程创建connector client进行连接，从而访问或修改MBeanServer中的MBean

### 使用JMX

#### JMXMP vs RMI
- RMI（不推荐）：如果client与JMX server之间没有防火墙，那么没问题。否则，就会涉及到穿越防火墙的问题。
>>>
    如果JMX采用rmi作为传输协议的话，客户端需要进行两个连接，如：JMXConnectorServer的JMXServiceURL为如下形式：
    service:jmx:rmi://localhost:5000/jndi/rmi://localhost:6000/jmxrmi
    
    首先client需要连接到rmiregistry上得到真实服务器的stub（如rmi://localhost:6000/rmxrmi，6000为注册端口），然后再根据该stub连接到真实的服务器上（如rmi://localhost:5000，5000为通信端口）。
    
    如果jmx服务端省略了通信端口（`localhost:5000`）即，`service:jmx:rmi:///jndi/rmi://localhost:6000/jmxrmi`，那么默认的通信端口是随机产生的。所以如果有防火墙，JMX服务器需要使用固定的通信端口，并且防火墙必须开放注册端口和通信端口两个端口。

- JMXMP：为避开rmi穿越防火墙问题，通常采用jmx消息协议(jmxmp)来代替rmi，使用jmxmp协议较为简单，并且服务端不需要进行端口注册过程。

##### 使用JMXMP
需要依赖于`jmxremote-optional.jar`包，但JDK默认不包含`jmxremote-optional.jar`。需要下载后拷贝到`$JAVA_HOME/jre/lib/ext/`目录下，或者如果你使用`gradle`等工具，添加一下依赖到你的`build.gradle`：
>>> 
    compile group: 'javax.management', name: 'jmxremote_optional', version: '1.0.1_04'

启用JMXMP
>>>
    -Djavax.management.builder.initial= 
    -Dcom.sun.management.jmxremote 
    -Dcom.sun.management.jmxremote.port=8855 
    -Dcom.sun.management.jmxremote.authenticate=false 
    -Dcom.sun.management.jmxremote.ssl=false

JMXConnectorServer的JMXServiceURL（可通过JConsole连接）
>>>
    service:jmx:jmxmp://host:port
