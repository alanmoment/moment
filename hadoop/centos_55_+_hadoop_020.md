# CentOS 5.5 + Hadoop 0.20

緊接著再來一發舊的，這一篇已經是去年的記錄了，當時是因為公司想要導入[Hadoop](http://hadoop.apache.org/)分析使用者行為，所以我才開始著手研究，就趁這個時候把他整理一下吧，現在的版本都已經不知道升到多少去了。

### 建立Hadoop基本環境

下載[Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)並開始安裝，設置Java使用環境必須要1.6以上，並且重新載入。

	$ ./jdk-7u21-linux-i586.rpm
	find / -name java
	/usr/java/jdk1.x.x-xx
	$ ln -s /usr/java/jdk1.x.x-xx /usr/java/jdk
	$ vim ~/.bashrc
	export JAVA_HOME=/usr/java/jdk
	export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
	export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH:$HOME/bin
	export HADOOP_HOME=/opt/hadoop
	$ source ~/.bashrc

建立hadoop帳號，設定SSH，切換為 hadoop 身分，或直接使用root

	$ yum -y install openssh
	$ su  hadoop
	$ passwd  hadoop
	$ ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa
	Generating public/private dsa key pair.
	Enter file in which to save the key (/home/ hadoop/.ssh/id_dsa):
	Enter passphrase (empty for no passphrase):   > ~/.ssh/authorized_keys 
	$ chmod 600 .ssh/authorized_keys
	$ ssh hadoop@localhost

找一個讓Hadoop安身立命的好地方，這邊我是放在opt底下，下載後解壓縮
	
	$ cd /opt
	$ wget 'http://apache.ntu.edu.tw/hadoop/core/hadoop-0.20.2/hadoop-0.20.2.tar.gz'
	$ tar xzvf hadoop-0.20.2.tar.gz
	$ mv hadoop-0.20.2 hadoop

設置hadoop-env.sh在文件最末端加上

	$ vim /opt/hadoop/conf/hadoop-env.sh
	export JAVA_HOME=/usr/java/jdk1.x.x-xx

測試一下hadoop可否執行

	$ /opt/hadoop/bin/hadoop
	Usage: hadoop [--config confdir] COMMAND
	where COMMAND is one of:
	...以下省略

測試 Local (Standalone) Mode，此模式下每個 Hadoop daemon 執行在一個分離的 Java 程序中。

	$ mkdir /opt/hadoop/input
	$ cp /opt/hadoop/conf/*.xml input
	$ bin/hadoop jar hadoop-*-examples.jar grep input output 'dfs[a-z.]+'
	10/02/22 10:47:42 INFO jvm.JvmMetrics: Initializing JVM Metrics with processName
	以下省略
	$ cat /opt/hadoop/output/*
	1       dfsadmin

更改config檔

	$ vim /opt/hadoop/conf/core-site.xml
	<configuration><property><name>fs.default.name</name><value>hdfs://localhost:9000</value></property></configuration>

	$ vim /opt/hadoop/conf/hdfs-site.xml
	<configuration><property><name>dfs.replication</name><value>1</value></property></configuration>

	$ vim /opt/hadoop/conf/mapred-site.xml
	<configuration><property><name>mapred.job.tracker</name><value>localhost:9001</value></property></configuration>

格式化分散式檔案系統

	$ /opt/hadoop/bin/hadoop namenode -format
	10/02/22 11:02:36 INFO namenode.NameNode: STARTUP_MSG:
	/************************************************************
	...以下省略

啟動 hadoop daemons

	$ /opt/hadoop/bin/start-all.sh
	namenode running as process 17390. Stop it first.
	localhost: datanode running as process 17514. Stop it first.
	...以下省略

### 瀏覽管理介面並開始測試

NameNode：http://localhost:50070/

JobTracker：http://localhost:50030/

複製檔案到分散式檔案系統

	$ /opt/hadoop/bin/hadoop fs -put conf input

執行範例jar檔測試是否正常

	$ /opt/hadoop/bin/hadoop jar hadoop-*-examples.jar grep input output 'dfs[a-z.]+'
	10/02/22 11:10:07 INFO mapred.FileInputFormat: Total input paths to process : 13
	10/02/22 11:10:07 INFO mapred.JobClient: Running job: job_201002221103_0001
	...以下省略

從分散式檔案系統拷貝檔案到本機檔案系統檢驗

	$ /opt/hadoop/bin/hadoop fs -get output output
	$ cat output/*
	cat: output/output: Is a directory
	1       dfsadmin

在分散式檔案系統上檢驗輸出檔案

	$ /opt/hadoop/bin/hadoop fs -cat output/*
	cat: File does not exist: output/output
	3       dfs.class
	2       dfs.period
	1       dfs.file
	1       dfs.replication
	1       dfs.servers
	1       dfsadmin
	1       dfsmetrics.log

停止hadoop

	$ /opt/hadoop/bin/stop-all.sh
	stopping jobtracker
	localhost: stopping tasktracker
	stopping namenode
	localhost: stopping datanode
	localhost: stopping secondarynamenode

## 發生錯誤

1. 有關於IP位址的都需要用網域取代否則會讀不到

2. 若啟動時出現要輸入密碼，是因為沒有authorized_keys

參考教學：

[http://sls.weco.net/CollectiveNote20/MR](http://sls.weco.net/CollectiveNote20/MR)

[http://forum.icst.org.tw/phpbb/viewtopic.php?f=10&t=17974](http://forum.icst.org.tw/phpbb/viewtopic.php?f=10&t=17974)

> Jun 13th, 2013 11:12:00pm