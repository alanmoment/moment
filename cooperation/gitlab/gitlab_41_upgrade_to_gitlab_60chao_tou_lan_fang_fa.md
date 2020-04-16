# Gitlab 4.1 upgrade to Gitlab 6.0超偷懶方法

[Gitlab](http://blog.gitlab.org/)官方網站已經釋出[Gitlab 6.0](http://blog.gitlab.org/gitlab-6-dot-0-released/)了，我的版本卻還在4.1。所以忍不住手癢，試試看4.1升級到6.0，但是這中間的版本實在太多啦。要一個一個升級，不知道又會遇到什麼問題。所幸愛亂搞的我，想到了妙招，如果我重新安裝6.0但是使用的是原本的4.1資料表，是不是也可以正常運作呢? 因為Gitlab的migrate應該是不會影響到以前的資料。

備份以下東西，以防止不能運作後，還能回復成4.1的版本

1. Mysql中的gitlabhq_production資料庫
2. /etc/init.d/gitlab

再來就先停止Gitlab的運行

	$ service gitlab stop
	
然後依照[官網](https://github.com/gitlabhq/gitlabhq/blob/6-0-stable/doc/install/installation.md)的安裝步驟。但只執行到`bundle install`。因為要使用到原本的資料庫。

	bundle install --deployment --without development test postgres aws

再來依照官網[5.4升級到6.0](https://github.com/gitlabhq/gitlabhq/blob/master/doc/update/5.4-to-6.0.md)的文件，執行下列程序。

	sudo -u git -H bundle exec rake db:migrate RAILS_ENV=production
	sudo -u git -H bundle exec rake migrate_groups RAILS_ENV=production
	sudo -u git -H bundle exec rake migrate_global_projects RAILS_ENV=production
	sudo -u git -H bundle exec rake migrate_keys RAILS_ENV=production
	sudo -u git -H bundle exec rake migrate_inline_notes RAILS_ENV=production
	sudo -u git -H bundle exec rake gitlab:satellites:create RAILS_ENV=production
	
	# Clear redis cache
	sudo -u git -H bundle exec rake cache:clear RAILS_ENV=production
	
	# Clear and precompile assets
	sudo -u git -H bundle exec rake assets:clean RAILS_ENV=production
	sudo -u git -H bundle exec rake assets:precompile RAILS_ENV=production

取得啟動腳本

	curl --output /etc/init.d/gitlab https://raw.github.com/gitlabhq/gitlab-recipes/master/init.d/gitlab
	chkconfig --add gitlab
	chkconfig gitlab on
	chmod +x /etc/init.d/gitlab

因為我的gitlab是掛在apache上的所以還要修改httpd-vhosts.conf

	$ vim /etc/httpd/conf.d/httpd-vhosts.conf
	<virtualhost>
	  ServerName git.ocomm.com.tw
	  DocumentRoot /home/git/gitlab/public
	
	  # !!! Be sure to point DocumentRoot to 'public'!
	  <directory>
	    # This relaxes Apache security settings.
	    AllowOverride all
	    # MultiViews must be turned off.
	    Options -MultiViews
	  </directory></virtualhost>

重新啟動apache

	$ service httpd restart

重新啟動gitlab

	$ service gitlab start

真的成功了...哈!!

![gitlab 6.0](https://lh6.googleusercontent.com/-f_Ruym_yx-k/Uik-NeJFm9I/AAAAAAAAAMo/xZzJxjPDpkU/w519-h466-no/gitlab6.0.PNG)

這中間遇到了一些問題，因為redis我有另外使用，所以沒有bind IP，也開了requirepass致使我的redis一直無法被gitlab正常使用，最後修改redis.conf才可以正常安裝。

	$ vim /etc/redis/redis.conf
	bind 127.0.0.1 #加回來
	#requirepass *redis* #註解起來否則gitlab無法正常使用

以及升級完6.0後在clone一直會問git這個使用者的密碼，在[這邊](https://github.com/gitlabhq/gitlab-public-wiki/wiki/Trouble-Shooting-Guide)也有提到這個問題。必須在authorized_keys加上這一段就不會再問了。

	$ vim /home/git/.ssh/authorized_keys
	command="/home/git/gitlab/apps/gitlab/gitlab-shell/bin/gitlab-shell key-2",no-port-forwarding,no-X11-forwarding,no-agent-forwarding,no-pty
	
> Sep 6th, 2013 10:53:00am