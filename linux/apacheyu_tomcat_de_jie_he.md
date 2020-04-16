# Apache與Tomcat的結合

因為我很愛東搞西搞，去年寫了個網站，但是是用JSP寫的，用了Status, Hibernate, tiles的框架，所以得佈署在Tomcat，可是80 port已經被Apache佔用了，Tomcat只能用8080，但看了我實在很不舒服，所以又開始想東想西了。

一開始，我想為Tomcat裝Plugin看能不能融入Apache，讓Apache呼叫Tomcat來執行程式。所以我找到了[mod_js](http://tomcat.apache.org/download-connectors.cgi)試著安裝之後，我失敗了(其實是偷懶!!)，後來有人跟我提到[php-java-bridge](http://php-java-bridge.sourceforge.net/)，但是還沒裝我就放棄了，因為我覺得根本不用這麼複雜...

Google到爛掉我終於找到一個太符合我這個懶人的方式了..非常簡單到爆掉! [mod_proxy](http://httpd.apache.org/docs/2.2/mod/mod_proxy.html)是我的救星。官網所寫的，必須開放Apache的以下模組

- mod_proxy
- mod_proxy_http
- mod_proxy_ftp
- mod_proxy_ajp
- mod_proxy_balancer

然後在http.conf加入設定(我的是httpd-vhosts.conf)

	$ vim /etc/httpd/conf.d/httpd-vhosts.conf
	<virtualhost>
		ServerName school.ocomm.com.tw
		ProxyPass / http://school.ocomm.com.tw:8080/
		ProxyPassReverse / http://school.ocomm.com.tw:8080/
	</virtualhost>

然後

	$ service httpd restart

完成....用到現在沒甚麼問題，等有問題再說吧!! 哈...

簡單到爆炸阿....我的網站[http://school.ocomm.com.tw](http://school.ocomm.com.tw)，8080不見了，看了都覺得開心。

> Aug 2nd, 2013 10:14:00am