### 资源地址

[hadoop官网](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html)

### 核心目录

```
bin 
etc  
include 
lib  
libexec 
LICENSE.txt 
NOTICE.txt  
README.txt  
sbin:存在各种脚本
share：各种目录

```

## wordcount示例

```shell
#wordcount 中创建word.txt wcoutput必须不存在
./bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount wcinput/ wcoutput
```

