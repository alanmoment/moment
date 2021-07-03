# Gitlab 4.1 upgrade to Gitlab 6.0 超偷懶方法

[Gitlab](http://blog.gitlab.org/)官方網站已經釋出[Gitlab 6.0](http://blog.gitlab.org/gitlab-6-dot-0-released/)了，我的版本卻還在 4.1。所以忍不住手癢，試試看 4.1 升級到 6.0，但是這中間的版本實在太多啦。要一個一個升級，不知道又會遇到什麼問題。所幸愛亂搞的我，想到了妙招，如果我重新安裝 6.0 但是使用的是原本的 4.1 資料表，是不是也可以正常運作呢? 因為 Gitlab 的 migrate 應該是不會影響到以前的資料。

備份以下東西，以防止不能運作後，還能回復成 4.1 的版本

1. Mysql 中的 gitlabhq_production 資料庫
2. /etc/init.d/gitlab

再來就先停止 Gitlab 的運行

```bash
service gitlab stop
```

然後依照[官網](https://github.com/gitlabhq/gitlabhq/blob/6-0-stable/doc/install/installation.md)的安裝步驟。但只執行到`bundle install`。因為要使用到原本的資料庫。

```bash
bundle install --deployment --without development test postgres aws
```

再來依照官網[5.4 升級到 6.0](https://github.com/gitlabhq/gitlabhq/blob/master/doc/update/5.4-to-6.0.md)的文件，執行下列程序。

```bash
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
```

取得啟動腳本

```bash
curl --output /etc/init.d/gitlab https://raw.github.com/gitlabhq/gitlab-recipes/master/init.d/gitlab
chkconfig --add gitlab
chkconfig gitlab on
chmod +x /etc/init.d/gitlab
```

因為我的 gitlab 是掛在 apache 上的所以還要修改 httpd-vhosts.conf

```bash
vim /etc/httpd/conf.d/httpd-vhosts.conf
```

```conf
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
```

重新啟動 apache

```bash
service httpd restart
```

重新啟動 gitlab

```bash
service gitlab start
```

真的成功了...哈!!

![gitlab 6.0](/assets/cooperation/gitlab/gitlab_41_upgrade_to_gitlab_60chao_tou_lan_fang_fa/gitlab6.0.PNG))

這中間遇到了一些問題，因為 redis 我有另外使用，所以沒有 bind IP，也開了 requirepass 致使我的 redis 一直無法被 gitlab 正常使用，最後修改 redis.conf 才可以正常安裝。

```bash
vim /etc/redis/redis.conf
```

```conf
bind 127.0.0.1 #加回來
#requirepass *redis* #註解起來否則gitlab無法正常使用
```

以及升級完 6.0 後在 clone 一直會問 git 這個使用者的密碼，在[這邊](https://github.com/gitlabhq/gitlab-public-wiki/wiki/Trouble-Shooting-Guide)也有提到這個問題。必須在 authorized_keys 加上這一段就不會再問了。

```bash
vim /home/git/.ssh/authorized_keys
```

```conf
command="/home/git/gitlab/apps/gitlab/gitlab-shell/bin/gitlab-shell key-2",no-port-forwarding,no-X11-forwarding,no-agent-forwarding,no-pty
```

> Sep 6th, 2013 10:53:00am
