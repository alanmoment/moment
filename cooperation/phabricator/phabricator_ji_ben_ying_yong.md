# Phabricator 基本應用

## Create Maniphest

在這可建立 `Task` 指派給某一個人，目前發現最大的功用是當在 review code 的時候可以讓 commit 加入某一個 task。

## Create Herald Rule

在 `Herald` 的頁面 `Create Herald Rule` 建立一條新規則

**Commits**

Commits 的規則是當 repositories 有提交程式碼的時候皆會通知

**Differential Revisions**

Differential Revisions

**Maniphest Tasks**

Maniphest Tasks 的規則是當有建立或修改 task 皆會通知

### 規則隸屬

**Personal**

個人的規則

**Object**

版本庫的規則

**Global**

全域規則

### 設定規則

**Personal**

Conditions

When `all of` these conditions are met:
`Repository is any of test_repository`

還在摸索中，所以就先通用只要是版本庫的 commit 都通知我

Action

Take these actions every time this rule matches:

- `Add me to CC` : cc mail 通知我
- `Send me an email` : mail 通知我
- `Mark with flag` : 建立 flag，還可以選顏色
- `Trigger an Audit by me` : 通知我 review code
- `Do nothing` : 什麼都不做

** Maniphest Tasks**

Conditions

When `all of` these conditions are met:
`Assignee is any of alan`

task 則是設定指派給我的都通知我

Action

Take these actions every time this rule matches:

- `Assign task to me` : 指派給我自己

只有這個規則比較不一樣。

## Owners

若覺得要設定一堆規則很麻煩，有發現另外一個比較簡單的...

在 `Owners` 可以 `Create Package` 在這邊選擇 `Repository` 並設定 path。這樣當版本庫有異動的時候就都會通知。

## Email 設定

在 `Config` 有兩個地方可以設定 email 一個是 `Mail` 另一個是 `PHPMailer` 就選擇比較簡單的設定即可，我自己設定兩個...結果好像會發兩封信...

郵件伺服器

	phpmailer.mailer
	Configure mailer used by PHPMailer.
	Current Value: "smtp"

郵件伺服器 domain

	phpmailer.smtp-host
	Host for SMTP.
	Current Value: domain.com.tw

郵件伺服器 port

	phpmailer.smtp-port
	Port for SMTP.
	Current Value: 25

是否加密

	phpmailer.smtp-protocol
	Configure TLS or SSL for SMTP.
	Current Value: null

帳號

	phpmailer.smtp-user
	Username for SMTP.
	Current Value: "root"

密碼

	phpmailer.smtp-password
	Password for SMTP.

Phabricator 建置很久了這兩天才開始在學著用，覺得 [Phabricator][1] 對於 teamwork 的幫助不亞於 [Gitlab][2] ，有時候在合併版本發生衝突的時候才開始在看為什麼會有衝突，總覺得很不爽。所以若能即時知道，團隊裡有誰做了甚麼樣的提交，相信對於開發中以及開發完的上線都會很有幫助。

但是網路上的資訊有點少，要摸索清楚整個系統有點吃力，每一樣都要 `try error`，文章寫起來也有點凌亂，不管了，慢慢整理吧。

[1]: /post/94171590878/install-phabricator-and-run-on-the-gitlab
[2]: /post/94170890773/github-gitlab

> Jan 6th, 2014 11:36:00pm