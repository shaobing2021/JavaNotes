# Hbase
启动并进入hbase
cd /soft/hbase-2.2.7/bin
#启动hbase
[root@Master bin]# ./start-hbase.sh
[root@Master bin]# ./hbase shell
## Hbase shell
```shell
#help命令
hbase(main):001:0> help
#查看get帮助
hbase(main):004:0> help "get"
#查看表
hbase(main):005:0> list
#创建表
create 'student','info'
#查看表信息
hbase(main):008:0> describe 'student'
Table student is ENABLED  
student                                                                                                                       
COLUMN FAMILIES DESCRIPTION                                                                                                                   
{NAME => 'info', VERSIONS => '1', NEW_VERSION_BEHAVIOR => 'false', KEEP_DELETED_CELLS => 'FALSE', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', MIN_VE
RSIONS => '0', REPLICATION_SCOPE => '0', BLOOMFILTER => 'ROW', IN_MEMORY => 'false', COMPRESSION => 'NONE', BLOCKCACHE => 'true', BLOCKSIZE => '65536'}  
1 row(s)
Quota is disabled

#插入数据与更新数据是一样的，有就更新，没有就增加
put 'student','1001','info:sex','male'
put 'student','1002','info:sex','male'
put 'student','1001','info:name','zhangsan'
put 'student','10011','info:name','10011'
#报错
put 'student','1004','class:name','1004'

#两行，不同列分成了不同行展示而已
hbase(main):014:0> scan "student"
ROW                                     COLUMN+CELL                                                                                                      
 1001                                   column=info:name, timestamp=1635180558687, value=zhangsan                                                        
 1001                                   column=info:sex, timestamp=1635180556322, value=male                                                             
 1002                                   column=info:name, timestamp=1635180583924, value=lisi                                                            
 1002                                   column=info:sex, timestamp=1635180556430, value=male                                                             
2 row(s)

#扫描行，startrow必须大写(],起始1001，可以查到1002，不能查1003，但是10011能查到，这是按照ascii码查找
hbase(main):005:0> scan 'student',{STARTROW => '1001' ,STOPROW => '1003'}
ROW                                     COLUMN+CELL                                                                                                     
 1001                                   column=info:name, timestamp=1635180558687, value=zhangsan                                                       
 1001                                   column=info:sex, timestamp=1635180556322, value=male                                                            
 10011                                  column=info:name, timestamp=1635250732604, value=10011                                                          
 1002                                   column=info:name, timestamp=1635180583924, value=lisi                                                           
 1002                                   column=info:sex, timestamp=1635180556430, value=male  
```
## 查询及删除
```shell
#根据rowKey查
get 'student','1001'
#根据rowKey+列族查
get 'student','1001','info:name'

#统计表数量
count 'student'
#根据rowKey删除数据，必须使用deleteall，否则报错
deleteall 'student','1001'

#删除某rowkey的某一列数据
delete 'student','1002','info:sex'
#清空表，hbase中没有事务，先disable，再truncate该表
truncate 'student'

#删除表，必须先disable表，再drop表
disable 'student'
drop 'student'

#修改表
hbase(main):058:0> alter 'student',{NAME => 'info',VERSIONS = 4}
SyntaxError: (hbase):58: syntax error, unexpected tRCURLY
alter 'student',{NAME => 'info',VERSIONS = 4}

hbase(main):059:0> describe 'student'
Table student is ENABLED                                                                                                     
student                                                                                                                      
COLUMN FAMILIES DESCRIPTION                                                                                                  
{NAME => 'info', VERSIONS => '3', NEW_VERSION_BEHAVIOR => 'false', KEEP_DELETED_CELLS => 'FALSE', DATA_BLOCK_ENCODING => 'NON
E', TTL => 'FOREVER', MIN_VERSIONS => '0', REPLICATION_SCOPE => '0', BLOOMFILTER => 'ROW', IN_MEMORY => 'false', COMPRESSION 
=> 'NONE', BLOCKCACHE => 'true', BLOCKSIZE => '65536'}         
```

## hbase中版本
```shell
#在hbase2.7版本中，只会删除最新的
delete 'student','1001','info:name'
scan 'student'

#命名空间
hbase(main):085:0> list_namespace
NAMESPACE                                                                                                                    
default                                                                                                                      
hbase                                                                                                                        
my_namespace 
#创建命名空间
hbase(main):084:0> create_namespace 'my_namespace'
Took 0.2619 seconds
#在命名空间下创建表,必须先创建命名空间，否则报错
create 'my_namespace:emp','info','detail'
#删除命名空间完整步骤
hbase(main):093:0> disable 'my_namespace:emp'
Took 0.4550 seconds                                                                                                          
hbase(main):094:0> drop 'my_namespace:emp'
Took 0.2323 seconds                                                                                                          
hbase(main):095:0> drop_namespace 'my_namespace'
#扫描元数据表
scan 'hbase:meta'
```