# 吃足苦頭的Mssql

為了讓 CentOS 的 PHP 可以支援 Mssql & UTF-8 真是吃了不少苦頭，最終在安裝 FreeTDS 後加上設定檔終於如願以償...

	$ vim /etc/freetds.conf
	[global]
        	# TDS protocol version
        	tds version = 8.0
        	client charset = UTF-8
        	charset = UTF-8
        	
> Dec 29th, 2014 5:07:01pm