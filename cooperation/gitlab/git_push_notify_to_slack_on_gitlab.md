# Git Push Notify to Slack on Gitlab

已經用了許久的 `git push` 通知 Slack，在今天補一下筆記。

首先還是得先申請一個 WebHook Url ，可以參考這邊 [connect-to-slack-on-tessel](/tessel/connect_to_slack_on_tessel.md)。

Gitlab 在 `7.5.1` 版之後，就有支援與 Slack 串接，之前測試超久...都沒辦法通知，也沒有 Error Message。

在 Gitlab 上選擇要設定的 Project，然後選擇 Settings > Services 依照下圖這樣設定就完成囉！！

![](/assets/cooperation/gitlab/git_push_notify_to_slack_on_gitlab/gitlab-to-slack.png)

每次只要設定的 Project 有 `git push` 那就會推播相關資訊到 Slack Channel。

![](/assets/cooperation/gitlab/git_push_notify_to_slack_on_gitlab/slack-notify.png)

> May 4th, 2015 5:03:49pm
