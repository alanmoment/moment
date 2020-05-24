# 使用Sqoop將MySQL資料匯入Hbase

想要使用MapReduce運算Hbase的資料，但若是原本資料在別種資料庫該怎麼辦呢，在龐大的Hadoop家族中[Sqoop](http://sqoop.apache.org/)就是完成這一個任務，他有提供各種資料庫匯入至Hbase的功能。

因為我慣用MySQL所以還是拿MySQL當作範例，因為我是蠻久之前試玩的，所以是用舊的版本。他的安裝非常方便!!

### 安裝Sqoop

```
$ cd /opt
$ wget 'https://github.com/downloads/cloudera/sqoop/sqoop-1.2.0.tar.gz'
```

或是到官網[下載](http://www.apache.org/dyn/closer.cgi/sqoop/)

```
$ tar zxvf sqoop-1.2.0.tar.gz
$ mv sqoop-1.2.0 sqoop
```

下載[mysql-connector-java-5.1.15-bin.jar](http://www.java2s.com/Code/Jar/m/Downloadmysqlconnectorjava5115binjar.htm)並複製到/opt/sqoop/lib，然後就完成囉!! 接下來就測試看看是否能正常運作。

### 測試Sqoop

列出資料表

```
$ /opt/sqoop/bin/sqoop list-tables --connect jdbc:mysql://127.0.0.1/test -P --username test
```

匯入資料表

```
$ /opt/sqoop/bin/sqoop import --connect jdbc:mysql://127.0.0.1/test --username test --table table_name --hive-import
```

匯入資料表時下WHERE

```
$ /opt/sqoop/bin/sqoop import --connect jdbc:mysql://127.0.0.1/test --username test --table table_name --hive-import --query 'SELECT * FROM `table_name` where flow_no > 1 AND flow_no  但是我測試過後...無法轉換換行阿..看官網是說sqoop 1.3才有支援，但是hadoop 0.20 並不支援sqoop 1.3一整個無言。改天再測試。
```

使用Sqoop有出現錯誤也可以顯示

```
$ /opt/sqoop/bin/sqoop import --connect jdbc:mysql://127.0.0.1/test --username test --table table_name --hive-import --verbose
```

### Demo

因為操作Sqoop是有點煩瑣的，所以我自己用PHP寫了[Mysql匯入Hbase](http://demo.ocomm.com.tw/hadoop)的簡單操作界面。

## 發生錯誤 ##

無法從mysql匯入hbase

```
11/11/11 12:07:59 ERROR tool.ImportTool: Encountered IOException running import job: org.apache.hadoop.mapred.FileAlreadyExistsException:
Output directory table_name already exists
```

可以試試看刪除Hbase資料表

```
/opt/hadoop/bin/hadoop fs -rmr table_name
```

非單機模式下，必須寫成MySQL位址，不能用localhost

```
--connect jdbc:mysql://mysqlserver_IP/databaseName
```

沒有資料庫的權限會發生下面的錯誤

```
11/11/11 23:22:13 INFO mapred.JobClient: Task Id : attempt_201111112316_0001_m_000004_0, Status : FAILED
java.io.IOException: SQLException in nextKeyValue
at com.cloudera.sqoop.mapreduce.db.DBRecordReader.nextKeyValue(DBRecordReader.java:238)
at org.apache.hadoop.mapred.MapTask$NewTrackingRecordReader.nextKeyValue(MapTask.java:423)
at org.apache.hadoop.mapreduce.MapContext.nextKeyValue(MapContext.java:67)
at org.apache.hadoop.mapreduce.Mapper.run(Mapper.java:143)
at com.cloudera.sqoop.mapreduce.AutoProgressMapper.run(AutoProgressMapper.java:187)
at org.apache.hadoop.mapred.MapTask.runNewMapper(MapTask.java:621)
at org.apache.hadoop.mapred.MapTask.run(MapTask.java:305)
at org.apache.hadoop.mapred.Child.main(Child.java:170)
Caused by: com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Table 'test.tbl_skw_buyer' doesn't exist
at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:39)
at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:27)
at java.lang.reflect.Constructor.newInstance(Constructor.java:513)
at com.mysql.jdbc.Util.handleNewInstance(Util.java:407)
at com.mysql.jdbc.Util.getInstance(Util.java:382)
at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:1052)
at com.mysql.jdbc.MysqlIO.checkErrorPacket(MysqlIO.java:3603)
at com.mysql.jdbc.MysqlIO.checkErrorPacket(MysqlIO.java:3535)
at com.mysql.jdbc.MysqlIO.sendCommand(MysqlIO.java:1989)
at com.mysql.jdbc.MysqlIO.sqlQueryDirect(MysqlIO.java:2150)
at com.mysql.jdbc.ConnectionImpl.execSQL(ConnectionImpl.java:2626)
at com.mysql.jdbc.PreparedStatement.executeInternal(PreparedStatement.java:2119)
at com.mysql.jdbc.PreparedStatement.executeQuery(PreparedStatement.java:2281)
at com.cloudera.sqoop.mapreduce.db.MySQLDataDrivenDBRecordReader.executeQuery(MySQLDataDrivenDBRecordReader.java:50)
at com.cloudera.sqoop.mapreduce.db.DBRecordReader.nextKeyValue(DBRecordReader.java:225)
... 7 more
```

> Jul 18th, 2013 2:54:00pm