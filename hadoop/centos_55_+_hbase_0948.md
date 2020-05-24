# CentOS 5.5 + Hbase 0.94.8

本篇與[CentOS 5.5 + Hadoop 0.20](/post/94169976403/centos-5-5-hadoop-0-20)其實是同一時期的，Hbase有別於RDBMS資料庫，底層是使用了分散式的檔案系統\(Hadoop HDFS)，他把不同的表拆成很多份，由不同的伺服器各自負責存取部分的資料，藉由這樣達到分散式架構，提高效能。詳細功能[官網](http://hbase.apache.org/)都有說明喔!

本篇預設環境：

1. 已經安裝了Java 1.6.x,或以後的版本
2. hosts改為slave

### 建立Hbase

一樣為Hbase找個家吧，從官網下載下來。

	$ cd /opt
	$ wget 'http://apache.cdpa.nsysu.edu.tw/hbase/stable/hbase-0.94.8.tar.gz'
	$ mv hbase-0.94.8 hbase
	$ mkdir -p /usr/local/hbase
	$ chown -R $USER:$USER /opt/hbase
	
進入conf配置hbse-env.sh

	$ vim /opt/hbase/conf/hbase-env.sh
	export HBASE_HOME=/opt/hbase
	export JAVA_HOME=/usr/java/jdk1.x.x_xx
	export HBASE_MANAGES_ZK=true #用Hbase的zookeeper

進入conf配置regionservers替换其中内容

	$ vim /opt/hbase/conf/regionservers
	slave

進入conf配置hbase-site.xml

	$ vim /opt/hbase/conf/hbase-site.xml
	<configuration><property><name>hbase.rootdir</name><value>hdfs://slave:9000/hbase</value><description>The directory shared by region servers.</description></property><property><name>hbase.tmp.dir</name><value>/var/hadoop/hbase-${user.name}</value><description>Temporary directory on the local filesystem.</description></property><property><name>hbase.cluster.distributed</name><value>true</value><description>The mode the cluster will be in. Possible values are false: standalone and pseudo-distributed setups with managed Zookeeper true: fully-distributed with unmanaged Zookeeper Quorum (see hbase-env.sh)</description></property><property><name>hbase.zookeeper.property.clientPort</name><value>2222</value><description>Property from ZooKeeper's config zoo.cfg.</description></property><property><name>hbase.zookeeper.quorum</name><value>slave</value><description>Comma separated list of servers in the ZooKeeper Quorum.</description></property></configuration>

這樣就配置完成了，接下來就啟動Hbase吧

	$ /opt/hbase/bin/start-hbase.sh
	root@localhost's password:
	localhost: starting zookeeper, logging to /usr/local/hbase/bin/../logs/hbase-root-zookeeper-localhost.localdomain.out
	starting master, logging to /usr/local/hbase/logs/hbase-root-
	...以下省略

檢查啟動狀況

	$ ps aux | grep hbase
	root     24578  3.0  2.7 1191892 28504 ?       Sl   14:07   0:01 /usr/java/jdk1.6.0_23/bin/java -Xmx1000m -XX:+HeapDumpOnOutOfMemoryError -XX:+UseConcMarkSweepGC -XX:+CMSIncrementalMode -XX:
	...以下省略

進入管理模式執行help說明

	$ /opt/hbase/bin/hbase shell
	HBase Shell; enter 'help<return>' for list of supported commands.
	Version: 0.94.8, r965666, Mon Jul 19 16:54:48 PDT 2012
	$ hbase(main):001:0> help
	HBASE SHELL COMMANDS:
	...以下省略

停止Hbase

	$ /opt/hbase/bin/stop-hbase.sh
	stopping master..................
	localhost: stopping zookeeper.

這些只是個開端而已...當初研究的時候Hadoop根本就是一個勢力非常龐大的集團，每個角色(套件)都各司其職，沒有了誰這個集團就什麼也不是，下一次會介紹Hadoop + Hbase的叢集環境配置文章，整理完又有Hive..整理不完吶~~吶~~吶~~

## 發生錯誤 ##

如果Hbase發生無法put試試看

	$ /opt/hadoop/bin/hadoop dfs -rmr input
	$ /opt/hadoop/bin/hadoop dfs -put conf input

或者把/tmp/hadoop-root的目錄刪掉

參考教學：

[http://forum.icst.org.tw/phpbb/viewtopic.php?f=21&t=19565](http://forum.icst.org.tw/phpbb/viewtopic.php?f=21&t=19565)

> Jun 15th, 2013 12:57:00am