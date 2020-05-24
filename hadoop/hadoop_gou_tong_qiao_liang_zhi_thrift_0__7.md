# Hadoop 溝通橋梁之 Thrift 0.7

會建置[Thrift](http://thrift.apache.org/)是因為我希望用PHP向[Hadoop + Hbase 叢集環境](/post/94170575263/hadoop-hbase)溝通。Thrift是全世界第一大國Facebook釋出的開源項目之一，主要用來串接不同語言，讓他們之間能互相溝通。

這裡我就是用來讓PHP也能使用MapReduce和Hbase溝通的。在安裝的時候吃了不少苦頭阿...腦細胞死了不少。就算現在再架設一遍，我相信會再痛苦一次的。

### 檢查並安裝依賴的函式庫

```
$ yum search libboost
```

> libboost c++的函式庫

```
$ yum search yacc
$ yum search flex
```

> make時會用到

```
$ yum search autoconf
$ yum search automake 
```

> automake 1.9以上actomake必須安裝2.6以上版本否則Compiler出錯。如果没有可自行編譯安装

```
$ yum search libtool
$ yum search flex
$ yum search bison
$ yum search g++
$ yum search gcc-c++
$ yum search python-devel
$ yum search libevent-devel
$ yum search zlib-devel
$ yum search ruby-devel

$ yum install automake libtool flex bison pkgconfig g++ gcc-c++ boost-devel libevent-devel zlib-devel python-devel ruby-devel
```

安裝全部套件後還是不能用再試試看，這個Compiler會超久

```
$ wget 'http://downloads.sourceforge.net/project/boost/boost/1.45.0/boost_1_45_0.tar.bz2'
$ tar jxf boost_1_45_0.tar.bz2
$ cd boost_1_45_0
Build Boost:
$ ./bootstrap.sh
$ ./bjam
```

### 建立Thrift

官網[下載](http://thrift.apache.org/download/)或是

```
$ cd /opt
$ wget 'http://apache.stu.edu.tw//thrift/0.7.0/thrift-0.7.0.tar.gz'
$ mv thrift-0.7.0 /opt/thrift
```

沒錯，我通通都放到opt底下囉。

> 5.0以後的版本應該不用底下的檢查了(可見得我已經記錄多久了) 
> 檢查/opt/thrift/底下有沒有bootstrap.sh，沒有再複製

``` 
$ cp /opt/thrift/contrib/fb303/bootstrap.sh /opt/thrift/
```

執行bootstrap.sh

``` 
$ ./bootstrap.sh
$ cd /opt/thrift
$ cp -r /opt/hbase/src/main/java/org/apache/hadoop/hbase/thrift ./hbase_thrift_src
$ cd hbase_thrift_src
```

### Generate code

```
$ thrift --gen php /opt/hbase/src/main/resources/org/apache/hadoop/hbase/thrift/Hbase.thrift
```

### 檢查環境

檢查hbase-env.sh

```
$ vim /opt/hbase/conf/hbase-env.sh
export JAVA_HOME=/usr/java/jdk1.6.0_26
```

檢查.bashrc

```
$ vim ~/.bashrc
JAVA=$JAVA_HOME/bin/java
export JAVA_HOME=/usr/java/jdk1.6.0_26
```

忘了的同學請回顧[CentOS 5.5 + Hadoop](http://alanmoment.ocomm.com.tw/post/94169976403/centos-5-5-hadoop-0-20)

### 複製產生的PHP程式碼到專案

複製到/var/www/hbase內

```
$ mkdir /var/www/hbase
$ chown $USER:$USER /var/www/hbase
$ cp -r /opt/thrift/lib/php/src /var/www/hbase/thrift
$ mkdir /var/www/hbase/thrift/packages
$ cp -r /opt/thrift/hbase_thrift_src/gen-php/* /var/www/hbase/thrift/packages/
```

### 啟動Thrift

```
$ /opt/hbase/bin/hbase thrift start
```

要在背景執行請在start加上&

### 測試

複製DemoClient.php 到測試目錄/var/www/hbase/

```
$ cp /opt/hbase/src/examples/thrift/DemoClient.php /var/www/hbase/DemoClient.php
```

## 發生錯誤 ##

發生過無法啟動的時候，我是重新複製lib

```
$ rm /opt/hbase/lib/hadoop-core-0.20-*
$ cp /opt/hadoop/hadoop-0.20.2-core.jar  ./
```

參考教學：

[http://sites.google.com/site/waue0920/Home/hbase/hadoop-hbase-thrift-php-an-zhuang-she-ding-cheng-shi-she-ji](http://sites.google.com/site/waue0920/Home/hbase/hadoop-hbase-thrift-php-an-zhuang-she-ding-cheng-shi-she-ji)

[http://blog.sina.com.cn/s/blog_5dce657a0100f0ou.html](http://blog.sina.com.cn/s/blog_5dce657a0100f0ou.html)

> Jun 18th, 2013 11:57:00am