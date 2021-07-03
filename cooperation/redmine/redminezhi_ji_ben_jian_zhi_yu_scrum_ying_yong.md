# Redmine 之基本建置與 Scrum 應用

工作上一直都有在應用 [Redmine](http://www.redmine.org/) 今天嘗試自己建立，所以又開始在亂搞了! 其實是最近自己做一個專案，也想試著管理一次 Scrum。

在 [RedmineInstall](http://www.redmine.org/projects/redmine/wiki/RedmineInstall) 有很詳細的安裝流程，安裝過程我也沒遇到甚麼困難的地方。所以在這邊就先略過了。

因為我自己建立的服務太多了，我習慣了 Apache 所以這次一樣是用 [mod_proxy](/linux/apacheyu_tomcat_de_jie_he.md) 的方式建立服務。

另外就是紀錄一套 Redmine 的 plugin。 [Backlogs](http://www.redminebacklogs.net/en/installation.html) 對 Scrum 有不錯的支援。有興趣可以安裝看看。

## 整合 Git Repository

Redmin 只能存取 local 端的 repository 所以沒辦法用 `git@git.ocomm.com.tw` 這種方式存取遠端。

```bash
mkdir /home/git/mirrors
cd /home/git/mirrors
git clone --mirror /home/git/mirrors/repo.git
```

設定權限給 `git`

```bash
chown -R git:git repo.git
cd repo.git
```

建立 `post-receive`

```bash
vim post-receive
```

```shell
#!/bin/sh
/usr/bin/git push --mirror /home/git/mirrors/repo.git
```

改變 `post-receive` 權限

```bash
chown git:git post-receive
chmod 700 post-receive
chmod a+rX -R ./
git config --add core.sharedRepository 644
```

最後在 Project > Settings > Repository 設定路徑。 Redmine 儲存機制需要一點時間處理資訊。

專案管理真的是一門學問，我才正要起步呢。

## Backlogs

```bash
cd /path/to/redmine/plugins
git clone git://github.com/backlogs/redmine_backlogs.git
cd redmine_backlogs
bundle exec rake db:migrate
cd /path/to/redmine
bundle exec rake redmine:backlogs:install
```

基本上這樣就完成了，官網有詳細的步驟。之後進入 Redmine 就會看到 Scrum 統計。

![backlog](https://lh6.googleusercontent.com/-lNoC-te7Lpk/UwR9YVA6ssI/AAAAAAAAAVw/4kYBrZKXi8A/w661-h565-no/backlog.png)

可以進入附加元件設置相關設定，另外我在網站管理的設定是以 Scrum 為主。

- 追蹤標籤清單
  1. bugs
  1. features
  1. supports
- 列舉值清單
  1. Design
  2. TDD
  3. Develop
  4. User test
  5. Release

我就是這樣開始使用，之後的設定再慢慢加。

這是以 Redmine 本身的問題清單來看

![scrum](https://lh4.googleusercontent.com/-zai2bPruPG4/UwSAVwIZywI/AAAAAAAAAWY/WAynE8WKSnM/w763-h435-no/scrum.png)

這個是以 Backlog 來看
![backlog-scrum](https://lh6.googleusercontent.com/-sKzOIeYYFko/UwSAV2xgIeI/AAAAAAAAAWc/4R44IrgFZ0s/w776-h175-no/scrum-1.png)

## Issue

1/8/2014 6:11:59 PM

寄發郵件不知道為什麼 `address` 設定自己的網域會寄不出去，會出現 `554 5.7.1 : relay access denied` 這個 error，我是在[官網](http://www.redmine.org/boards/1/topics/39783)找到答案的，把 `address` 改成 `localhost` 就可以了。

> Feb 18th, 2014 5:38:00am
