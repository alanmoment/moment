# 使用MapReduce之替代方案Hive

若要操作Hadoop就必須要會[MapReduce](http://hadoop.apache.org/docs/stable/mapred_tutorial.html)，很好!!在Hadoop很夯之前相信很多人都對MapReduce非常陌生，沒錯!!那真的是有點困難，但是呢，若我講SQL語言，相信你一定信心十足吧!!

因此囉，Facebook當初也了這個問題傷透了腦筋，因為要找一個熟悉MapReduce的工程師比找一個會SQL語法的工程師還困難的情況底下，他們想出了應變之道，那就是用極接近SQL語言的方式去操作MapReduce，因為找到會SQL語言的人較容易。

所以[Hive](http://hive.apache.org/)就是為此誕生的，這是Facebook一個部門的研發成果，後來為了讓更多人可以應用，他們將其釋出提供給Apache當作開源項目，讓Hive開始發揚光大，更多的介紹官網都有唷。

若你看不順眼Hive，你也可以選擇其他的諸如[PIG](http://pig.apache.org/)(我不是在罵人...真的叫PIG)，或是你真的想挑戰MapReduce當然也可以，任君挑選。因為我喜歡Hive的Logo所以囉，我就選他了。

### 建立Hive

從官網[下載](http://www.apache.org/dyn/closer.cgi/hive/)或是

```
$ cd /opt
$ wget 'http://ftp.stut.edu.tw/var/ftp/pub/OpenSource/apache//hive/hive-0.7.1/hive-0.7.1.tar.gz'
$ mv hive-0.7.1 /opt/hive
```

### 安裝ivy

```
$ cd /tmp
$ wget 'http://www.apache.org/dist/ant/ivy/2.3.0/apache-ivy-2.3.0-bin-with-deps.tar.gz'
$ tar zxvf apache-ivy-2.3.0-bin-with-deps.tar.gz
$ mv apache-ivy-2.3.0 /usr/local
$ ln -s apache-ivy-2.3.0 ivy
```

### 配置環境

```
$ vim ~/.bashrc
export HIVE_HOME=/opt/hive
export IVY_HOME=/usr/local/ivy
export PATH=$HIVE_HOME/bin
export HIVE_HOME
```

### 修改hive-default.xml

多個Hive節點的數據内容保存在HDFS上，通過配置文件，指向NameNode節點即可，例如：

```
$ vim /opt/hive/conf/hive-default.xml
<property><name>hive.metastore.warehouse.dir</name><value>hdfs://master:9000/user/hive/warehouse</value>/user/hive/warehouse –>
	<description>Master of default database for the warehouse</description></property>
```

### 使用Hive的三種Metastore儲存方式

1. 使用Derby資料庫儲存數據
2. 使用本機的MySQL
3. 使用遠端的MySQL

我自己熟悉的是MySQL當然就選他囉!!

```
$ vim /opt/hive/conf/hive-site.xml
<configuration><property><name>hive.metastore.local</name><value>true</value></property><property><name>javax.jdo.option.ConnectionURL</name><value>jdbc:mysql://localhost:3306/hive?useUnicode=true&characterEncoding=UTF-8</value><description>JDBC connect string for a JDBC metastore</description></property><property><name>javax.jdo.option.ConnectionDriverName</name><value>com.mysql.jdbc.Driver</value><description>Driver class name for a JDBC metastore</description></property><property><name>javax.jdo.option.ConnectionUserName</name><value>資料庫account</value><description>username to use against metastore database</description></property><property><name>javax.jdo.option.ConnectionPassword</name><value>資料庫password</value><description>password to use against metastore database</description></property></configuration>
```

```
$ vim /opt/hive/conf/jpox.properties
javax.jdo.PersistenceManagerFactoryClass=org.jpox.PersistenceManagerFactoryImpl
org.jpox.autoCreateSchema=false
org.jpox.validateTables=false
org.jpox.validateColumns=false
org.jpox.validateConstraints=false
org.jpox.storeManagerType=rdbms
org.jpox.autoCreateSchema=true
org.jpox.autoStartMechanismMode=checked
org.jpox.transactionIsolation=read_committed
javax.jdo.option.DetachAllOnCommit=true
javax.jdo.option.NontransactionalRead=true
javax.jdo.option.ConnectionDriverName=org.apache.derby.jdbc.ClientDriver
javax.jdo.option.ConnectionURL=jdbc:derby://localhost:1527/metastore_db;create=true
javax.jdo.option.ConnectionUserName=APP
javax.jdo.option.ConnectionPassword=mine
org.jpox.cache.level2=true
org.jpox.cache.level2.type=SOFT
```

有一個需要注意的地方是，需要把一個jar包mysql-connector-java-5.1.15-bin.jar複製到hive的lib目錄下才行，否則執行語句的時候會出錯，範例如下：

```
hive> show tables;
FAILED: Error in metadata: javax.jdo.JDOFatalInternalException: Error creating transactional connection factory
NestedThrowables:
java.lang.reflect.InvocationTargetException
FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask
```

> 我當時安装MYSQL的时候是使用RPM来安装的，没找到mysql-connector-java-5.1.15-bin.jar。

### 在MySQL建立Hive資料庫並且設定語系

```
character_set_server                = latin1
collation_server                    = latin1_swedish_ci
default_character_set               = latin1
```

### 讓Thrift使用Hive

修改/opt/hive/src/metastore/if/hive_metastore.thrift第6行，依實際路徑修改

```
include "/opt/thrift/contrib/fb303/if/fb303.thrift"
```

開始產生Thrift使用的Hive程式碼

```
$ mkdir /opt/thrift/packages/
$ cd /opt/thrift/packages/
```

Generate code並將目錄搬至packages

```
$ thrift --gen php /opt/thrift/contrib/fb303/if/fb303.thrift
$ cp -r gen-php/fb303 ./fb303
$ thrift --gen php -I include /opt/hive/src/metastore/if/hive_metastore.thrift
$ cp -r gen-php/hive_metastore ./hive_metastore
$ thrift --gen php -I include /opt/hive/src/service/if/hive_service.thrift
$ cp -r gen-php/hive_service ./hive_service
$ thrift --gen php -I include /opt/hive/src/ql/if/queryplan.thrift
$ cp -r gen-php/queryplan ./queryplan
```

修改/opt/hive/src/metastore/if/hive\_metastore.thrift第27行，依實際路徑修改

```
include "/opt/hive/src/metastore/if/hive_metastore.thrift"
include "/opt/hive/src/ql/if/queryplan.thrift"
```

### 新增hive-thrift執行檔

```
$ vim /etc/init.d/hive-thrift
#!/bin/bash
# init script for Hive Thrift Interface.
#
# chkconfig: 2345 90 10
# description: Hive Thrift Interface

# Source function library.
. /etc/rc.d/init.d/functions

# Paths to configuration, binaries, etc
HIVE_BIN=/usr/bin/hive
HIVE_ARGS="--service hiveserver"
HIVE_LOG=/var/log/hive-thrift.log
HIVE_USER="hadoop"
ANT_LIB=/usr/share/java

if [ ! -f $HIVE_BIN ]; then
	echo "File not found: $HIVE_BIN"
	exit 1
fi
	
# pid file for /sbin/runuser
pidfile=${PIDFILE-/var/run/hive-thrift.pid}
# pid file for the java child process.
pidfile_java=${PIDFILE_JAVA-/var/run/hive-thrift-java.pid}
RETVAL=0

start() {
	# check to see if hive is already running by looking at the pid file and grepping
	# the process table.
	if [ -f $pidfile_java ] && checkpid `cat $pidfile_java`; then
		echo "hive-thrift is already running"
		exit 0
	fi
	echo -n {1}quot;Starting $prog: "
	/sbin/runuser -s /bin/sh -c "$HIVE_BIN $HIVE_ARGS" $HIVE_USER >> $HIVE_LOG 2>&1 &
	runuser_pid=$!
	echo $runuser_pid > $pidfile
	# sleep so the process can make its way to the process table.
	usleep 500000
	# get the child Java process that /usr/bin/hive started.
	java_pid=$(ps -eo pid,ppid,fname | awk "{ if (\$2 == $runuser_pid && \$3 ~ /java/) { print \$1 } }")
	echo $java_pid > $pidfile_java
	disown -ar
	# print status information.
	ps aux | grep $java_pid &> /dev/null && echo_success || echo_failure
	RETVAL=$?
	echo
	return $RETVAL
}
	
stop() {
	# check if the process is already stopped by seeing if the pid file exists.
	if [ ! -f $pidfile_java ]; then
		echo "hive-thrift is already stopped"
		exit 0
	fi
	echo -n {1}quot;Stopping $prog: "
	if kill `cat $pidfile` && kill `cat $pidfile_java`; then
		RETVAL=0
		echo_success
	else
		RETVAL=1
		echo_failure
	fi
	echo
	[ $RETVAL = 0 ] && rm -f ${pidfile} ${pidfile_java}
}
	
status_fn() {
	if [ -f $pidfile_java ] && checkpid `cat $pidfile_java`; then
		echo "hive-thrift is running"
		exit 0
	else
		echo "hive-thrift is stopped"
		exit 1
	fi
}

case "$1" in
	start)
		start
		;;
	stop)
		stop
		;;
	status)
		status_fn
		;;
	restart)
		stop
		start
		;;
	*)
		echo {1}quot;Usage: $prog {start|stop|restart|status}"
		RETVAL=3
esac

exit $RETVAL
```

### 更改權限檔案

```
$ touch /var/run/hive-thrift.pid
$ touch /var/log/hive-thrift.log
$ touch /var/run/hive-thrift-java.pid
$ chown hadoop:hadoop /var/run/hive-thrift.pid
$ chown hadoop:hadoop /var/log/hive-thrift.log
$ chown hadoop:hadoop /var/run/hive-thrift-java.pid
```

### Hive的三種執行模式

**Hive CLI**

Hive CLI（Hive Command Line)Client可以以直接在命令行模式下進行操作。

**HWI**

hwi（Hive Web Interface，Hive Web接口），Hive提供了更友善的Web介面
	
在hive-site.xml的<configuration>添加項目

```
$ vim /opt/hive/conf/hive-site.xml
<property><name>hive.hwi.war.file</name><value>lib/hive-hwi-0.7.1.war</value><description>This sets the path to the HWI war file, relative to ${HIVE_HOME}. </description></property><property><name>hive.hwi.listen.port</name><value>9999</value><description>This is the port the Hive Web Interface will listen on</description></property>
```

啟動HWI服務

```
$ hive --service hwi
```

或是在背景啟動hwi服務

```
$ nohup hive --service hwi &
```

我的Hive部署在192.168.1.28，Hive默認HWI端口為9999。我们在瀏覽器中輸入http://192.168.1.28:9999/hwi/ 就可以瀏覽了

**hiveserver**

hiveserver，Hive提供了Thrift服務，Thrift Client目前支持C++/Java/PHP/Python/Ruby。

### 啟動與停止Hive

啟動

```
$ /opt/hive/bin/hive --service start-hive
```

停止

```
$ /opt/hive/bin/hive --service stop-hive
```

### Hive執行叢集環境

修改hive-site.xml

在的<configuration>添加項目

```
$ vim /opt/hive/conf/hive-site.xml
<property><name>hive.zookeeper.quorum</name><value>master,slave</value></property><property><name>hive.aux.jars.path</name><value>file:///opt/hive/lib/hive-hbase-handler-0.7.1.jar,file:///opt/hive/lib/zookeeper-3.3.2.jar,file:///opt/hive/lib/hbase-0.90.4.jar</value></property>
```

執行所有節點

```
$ /opt/hive/bin/hive –auxpath /opt/lib/hive-hbase-handler-0.7.1.jar,/opt/hive/lib/hbase-0.89.0-SNAPSHOT.jar,/opt/hive/lib/zookeeper-3.3.1.jar -hiveconf hbase.master=127.0.0.1:60000
```

或是背景執行

```
$ nohup /opt/hive/bin/hive --service hiveserver 9998 -hiveconf hbase.zookeeper.quorum=master,slave &
```

### 將hive-thrift新增至服務中並設開機啟動

```
$ chmod +x /etc/init.d/hive-thrift
$ chkconfig --add hive-thrift
$ chkconfig hive-thrift on
```

### 檢查是否有執行Hive

```
$ jps
```

如果有出現RunJar則執行成功

## 發生錯誤 ##

如果資料寫不進去試著把/tmp/root裡面的log刪掉

參考教學：

[http://blog.csdn.net/liuzhoulong/article/details/6441914](http://blog.csdn.net/liuzhoulong/article/details/6441914)

> Jun 23rd, 2013 10:03:00pm