# Hadoop + Hbase 叢集環境

這是配合著[CentOS 5.5 + Hadoop 0.20](/post/94169976403/centos-5-5-hadoop-0-20)以及[CentOS 5.5 + Hbase 0.94.8](/post/94170198668/centos-5-5-hbase-0-94-8)建立的叢集系統，為了方便記錄，所以我分開介紹囉。但記錄畢竟有點久了，有些步驟還需要再架一次才會更正確。

因為Hadoop本身的優勢將文件的儲存和任務處理分散化，Hadoop分散式架構中有兩種負責不同功能的服務器Master服務器和Slave服務器，安裝時假設要為2台服務器安裝Hadoop架構。

本篇環境介紹

1. 安裝了Java 1.6.x,或以後的版本
2. 兩台服務器名稱為master和slave
2. 兩台服務器操作系統均為centos5.*且版本大於等於5.4
3. master將作為master主服務器使用，slave將作為slave服務器使用:
4. master和slave的wget命令均可正常使用
5. master和slave均正常運行且可正確聯繫
6. master和slave空間足夠
7. master和slave均已獲取root權限
8. master的ip位址為192.168.1.28，slave的ip位址為192.168.1.29

### 設置環境變數

設置hosts在Master和slave的/etc/hosts下共同增加

	$ vim /etc/hosts
	192.168.1.28 master
	192.168.1.29 slave

修改master的hostname文件

	$ vim /etc/hostname
	master

修改slave的hostname文件

	$ vim /etc/hostname
	slave

### 免密碼登入遠端電腦

請參考[SSH免密碼登入遠端電腦](/post/94170433873/ssh免密碼登入遠端電腦)

### 重新配置Hadoop

配置core-site.xml，在<configuration>節點下添加

	$ vim /opt/hadoop/conf/core-site.xml
	<property><name>hadoop.tmp.dir</name><value>/home/hadoop-${user.name}</value></property><property><name>fs.default.name</name><value>hdfs://master:9000</value></property>

配置mapred-site.xml，在<configuration>節點下添加

	$ vim /opt/hadoop/conf/mapred-site.xml
	<property><name>mapred.job.tracker</name><value>master:9001</value></property>

修改master文件

	$ vim /opt/hadoop/conf/master
	master

修改slaves

	$ vim /opt/hadoop/conf/slaves
	slave

修改内容，注意不是添加是更改

### 重新配置Hbase

配置hbase-env.sh

	$ vim /opt/hbase/conf/hbase-env.sh
	export JAVA_HOME=/usr/lib/jvm/java-6-sun
	export HADOOP_CONF_DIR=/opt/hadoop/conf
	export HBASE_HOME=/opt/hbase
	export HBASE_LOG_DIR=/var/hadoop/hbase-logs
	export HBASE_PID_DIR=/var/hadoop/hbase-pids
	export HBASE_MANAGES_ZK=true
	export HBASE_CLASSPATH=$HBASE_CLASSPATH:/opt/hadoop/conf

配置hbase-site.xml，在<configuration>節點下添加

	$ vim /opt/hbase/conf/hbase-site.xml
	<configuration><property><name>hbase.rootdir</name><value>hdfs://master:9000/hbase</value><description>The directory shared by region servers.
		    </description></property><property><name>hbase.tmp.dir</name><value>/var/hadoop/hbase-${user.name}</value><description>Temporary directory on the local filesystem.</description></property><property><name>hbase.cluster.distributed</name><value>true</value><description>The mode the cluster will be in. Possible values are
		      false: standalone and pseudo-distributed setups with managed Zookeeper
		      true: fully-distributed with unmanaged Zookeeper Quorum (see hbase-env.sh)
		        </description></property><property><name>hbase.zookeeper.property.clientPort</name><value>2222</value><description>Property from ZooKeeper's config zoo.cfg.
		    </description></property><property><name>hbase.zookeeper.quorum</name><value>master,slave</value><description>Comma separated list of servers in the ZooKeeper Quorum.
		    </description></property><property><name>hbase.zookeeper.property.dataDir</name><value>/var/hadoop/hbase-data</value></property><property><name>hbase.master</name><value>hdfs://master:60000</value></property></configuration>

將hadoop的設定放到hbase內

	$ cd /opt/hbase/conf
	$ cp /opt/hadoop/conf/core-site.xml ./
	$ cp /opt/hadoop/conf/hdfs-site.xml ./
	$ cp /opt/hadoop/conf/mapred-site.xml ./

替换其中内容，如果有加在slave則會變成Datanode

	$ vim /opt/hbase/conf/regionservers
	slave

這樣就算一台Hbase的叢集環境了

### 安裝第二台電腦：slave

複製第一台電腦的所有設定到此台機器內，因此於第二台電腦下指令

	$ cd /opt/
	$ scp -R 192.168.1.28:/opt/hbase ./
	$ chown $USER:$USER ./hbase

在第一台電腦master電腦啟動Hbase叢集

	$ /opt/hbase/bin/start-hbase.sh

若沒有出現錯誤訊息，代表成功，完整的程序可用jps來看。在第一台電腦上執行jps可以看到

	$ jps
	HQuorumPeer
	HRegionServer
	NameNode
	HMaster
	JobTracker
	DataNode
	TaskTracker
	Jps

參考教學：

[https://sites.google.com/site/waue0920/Home/hbase/hbase-cong-ji-an-zhuang](https://sites.google.com/site/waue0920/Home/hbase/hbase-cong-ji-an-zhuang) 

> Jun 17th, 2013 2:32:00pm

> hadoop, hbase