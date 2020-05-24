# Gearman Job Worker for PHP

最近同事接觸到需要 worker 來處理一些非同步的事情。諸如訂單、寄發email都常用非同步來執行，以往這些事情大多都用排程解決，其實還有更好的 solution。他們使用 [Gearman][1] 來解決這個問題。好奇心的驅使，就算不是我實作的我也想去玩玩看新的東西，Gearman 可以在系統裡起一個 `Job Worker` 也可以算是 service，這個 job worker 可以在不起一個排程的情況底下，幫你寄信、處理訂單。也就是當你有需要使用 email 在呼叫他就好了。

## 安裝 Gearman 

在 CentOS 中可以直接用 `yum` 安裝

```
$ yum install -y gearmand
```

## 安裝 Gearman 依賴包

```
$ yum -y install re2c geoip geoip-data geoip-devel gcc* boost* libgearman*
```

這兩個步驟都很簡單

## 安裝 PHP 的 Gearman extension

```
$ wget http://pecl.php.net/get/gearman-1.1.1.tgz
$ cd gearman-1.1.1
$ phpize
$ ./configure
```

![](/assets/php/gearman_job_worker_for_php/gearman-1.PNG)

```
$ make && make install
```

![](/assets/php/gearman_job_worker_for_php/gearman-2.PNG)

![](/assets/php/gearman_job_worker_for_php/gearman-3.PNG)

最後在 `php.ini` 加上 module

```
$ vim /etc/php.ini
extension="gearman.so"
```

儲存後重新啟動

```
$ service httpd restart
```

這樣 php 就支援 gearman 囉

![](/assets/php/gearman_job_worker_for_php/gearman-4.PNG)

## Hello World

不免俗的一定要測試一下的，這邊我就利用官網提供的程式碼做測試。

首先建立一隻 worker.php

```
$ vim worker.php
<?php $worker= new GearmanWorker();
	$worker->addServer();
	$worker->addFunction("reverse", "my_reverse_function");
	while ($worker->work());
	
	function my_reverse_function($job)
	{
		return strrev($job->workload());
	}
?>
```

再建立一隻 client.php

```
$ vim client.php
<?php $client= new GearmanClient();
	$client->addServer();
	print $client->do("reverse", "Hello World!");
?>
```

然後執行 worker

```
$ php worker.php
```

再執行 client 就可以得到 Hello World 了

```
$ php client.php 
```

若要停止這個 job worker 可以用 `Ctrl + C`

這東西還蠻強大的，之前試用過 [Beanstalk][2] 覺得 Gearman 相對強大許多。

[1]: http://gearman.org/
[2]: http://beanstalkapp.com/

> Dec 22nd, 2013 4:40:00pm