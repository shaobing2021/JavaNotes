## hadoop

### 资源地址

[hadoop官网](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html)

### 核心目录

```shell
cd /estar/hadoop
bin 
etc  
include 
lib  
libexec 
LICENSE.txt 
NOTICE.txt  
README.txt  
sbin:存在各种脚本
share：各种目录,存放有
```

### wordcount示例

#### 本地

```shell
#wordcount 中创建word.txt wcoutput必须不存在
./bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount /wcinput /wcoutput
```

#### hdfs

```shell
#创建目录
hadoop fs -mkdir /input
#上传文件到hdfs
hadoop fs -put openjdk-8u41-b04-linux-x64-14_jan_2020.tar.gz /wcinput

```

## 启动

```
[estar@hadoopMaster hadoop-3.1.3]$ jps
4944 NodeManager
5170 Jps
1595 DataNode
4990 JobHistoryServer
1487 NameNode

[estar@hadoopNode1 sbin]$ jps
4881 ResourceManager
4999 NodeManager
5354 Jps
1373 DataNode


[estar@hadoopNode2 hadoop-3.1.3]$ jps
4040 Jps
1368 DataNode
1480 SecondaryNameNode
3903 NodeManager

```

## 核心配置文件

### core-site.xml

```xml
[estar@hadoopMaster hadoop]$ cat core-site.xml 
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->

<configuration>
!-- 指定 NameNode 的地址 -->
 <property>
     <name>fs.defaultFS</name>
     <value>hdfs://hadoopMaster:8020</value>
 </property>
 <!-- 指定 hadoop 数据的存储目录 -->
 <property>
     <name>hadoop.tmp.dir</name>
     <value>/estar/hadoop-3.1.3/data</value>
 </property>
 <!-- 配置HDFS网页登录使用的静态用户为estar -->
    <property>
        <name>hadoop.http.staticuser.user</name>
        <value>estar</value>
    </property>
</configuration>
```

### hdfs-site.xml

```xml
[estar@hadoopMaster hadoop]$ cat hdfs-site.xml 
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->

<configuration>
<!-- nn web 端访问地址-->
<property>
 <name>dfs.namenode.http-address</name>
 <value>hadoopMaster:9870</value>
 </property>
<!-- 2nn web 端访问地址-->
 <property>
 <name>dfs.namenode.secondary.http-address</name>
 <value>hadoopNode2:9868</value>
 </property>
</configuration>
```

### yarn-site.xml

```properties
[estar@hadoopMaster hadoop]$ cat yarn-site.xml 
<?xml version="1.0"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->
<configuration>

 <!-- 指定 MR 走 shuffle -->
 <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
 </property>
 <!-- 指定 ResourceManager 的地址-->
 <property>
    <name>yarn.resourcemanager.hostname</name>
    <value>hadoopNode1</value>
 </property>
 <!-- 环境变量的继承 -->
 <property>
    <name>yarn.nodemanager.env-whitelist</name>
    <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CO
NF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
 </property>
    <property>
        <name>yarn.nodemanager.resource.memory-mb</name>
        <value>22528</value>
        <discription>每个节点可用内存,单位MB</discription>
    </property>
 
    <property>
        <name>yarn.scheduler.minimum-allocation-mb</name>
        <value>1500</value>
        <discription>单个任务可申请最少内存，默认1024MB</discription>
    </property>
 
    <property>
        <name>yarn.scheduler.maximum-allocation-mb</name>
        <value>16384</value>
        <discription>单个任务可申请最大内存，默认8192MB</discription>
    </property>
</configuration>

```

### mapred-site.xml

```xml
[estar@hadoopMaster hadoop]$ cat mapred-site.xml 
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->

<configuration>
<!-- 指定 MapReduce 程序运行在 Yarn 上 -->
 <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
 </property>
<!-- 历史服务器端地址 -->
<property>
    <name>mapreduce.jobhistory.address</name>
    <value>hadoopMaster:10020</value>
</property>

<!-- 历史服务器web端地址 -->
<property>
    <name>mapreduce.jobhistory.webapp.address</name>
    <value>hadoopMaster:19888</value>
</property>
    <property>
        <name>mapreduce.map.memory.mb</name>
        <value>1500</value>
        <description>每个Map任务的物理内存限制</description>
    </property>
 
    <property>
        <name>mapreduce.reduce.memory.mb</name>
        <value>3000</value>
        <description>每个Reduce任务的物理内存限制</description>
    </property>
 
    <property>
        <name>mapreduce.map.java.opts</name>
        <value>-Xmx1200m</value>
    </property>
 
    <property>
        <name>mapreduce.reduce.java.opts</name>
        <value>-Xmx2600m</value>
    </property>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <!-- 开启日志聚集功能 -->
    <property>
      <name>yarn.log-aggregation-enable</name>
      <value>true</value>
    </property>
    <!-- 设置日志聚集服务器地址 -->
    <property>  
      <name>yarn.log.server.url</name>  
      <value>http://hadoop102:19888/jobhistory/logs</value>
    </property>
    <!-- 设置日志保留时间为7天 -->
    <property>
      <name>yarn.log-aggregation.retain-seconds</name>
      <value>604800</value>
    </property>

</configuration>
```



### worker

```xml
[estar@hadoopMaster hadoop]$ cat workers 
hadoopMaster
hadoopNode1
hadoopNode2
```

