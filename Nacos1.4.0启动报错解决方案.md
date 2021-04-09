# Nacos1.4.0启动报错解决方案

看了官网得知Nacos1.4.0环境要求，jdk1.8+，maven3.2.x+。我的操作系统是win10，避免大家采坑，所以写篇博客给需要帮助的人。

### 报错信息

```
Caused by: java.lang.UnsatisfiedLinkError: C:\Users\Administrator\AppData\Local\Temp\librocksdbjni6835459412041025213.dll: Can't find dependent libraries
        at java.lang.ClassLoader$NativeLibrary.load(Native Method)
        at java.lang.ClassLoader.loadLibrary0(ClassLoader.java:1941)
        at java.lang.ClassLoader.loadLibrary(ClassLoader.java:1824)
        at java.lang.Runtime.load0(Runtime.java:809)
        at java.lang.System.load(System.java:1086)
        at org.rocksdb.NativeLibraryLoader.loadLibraryFromJar(NativeLibraryLoader.java:78)
        at org.rocksdb.NativeLibraryLoader.loadLibrary(NativeLibraryLoader.java:56)
        at org.rocksdb.RocksDB.loadLibrary(RocksDB.java:64)
        at org.rocksdb.RocksDB.<clinit>(RocksDB.java:35)
        at com.alipay.sofa.jraft.storage.impl.RocksDBLogStorage.<clinit>(RocksDBLogStorage.java:75)
        at com.alipay.sofa.jraft.core.DefaultJRaftServiceFactory.createLogStorage(DefaultJRaftServiceFactory.java:50)
        at com.alipay.sofa.jraft.core.NodeImpl.initLogStorage(NodeImpl.java:545)
        at com.alipay.sofa.jraft.core.NodeImpl.init(NodeImpl.java:961)
        at com.alipay.sofa.jraft.core.NodeImpl.init(NodeImpl.java:137)
        at com.alipay.sofa.jraft.RaftServiceFactory.createAndInitRaftNode(RaftServiceFactory.java:47)
        at com.alipay.sofa.jraft.RaftGroupService.start(RaftGroupService.java:129)
        at com.alibaba.nacos.core.distributed.raft.JRaftServer.createMultiRaftGroup(JRaftServer.java:267)
        at com.alibaba.nacos.core.distributed.raft.JRaftProtocol.addLogProcessors(JRaftProtocol.java:163)
        at com.alibaba.nacos.naming.consistency.persistent.impl.PersistentServiceProcessor.init(PersistentServiceProcessor.java:149)
        at com.alibaba.nacos.naming.consistency.persistent.impl.PersistentServiceProcessor.<init>(PersistentServiceProcessor.java:143)
        at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
        at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
        at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
        at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
        at org.springframework.beans.BeanUtils.instantiateClass(BeanUtils.java:175)
        ... 142 common frames omitted
```

### 解决方案

下载并安装vc++ 2015 依赖库，地址：https://www.microsoft.com/zh-CN/download/details.aspx?id=48145

