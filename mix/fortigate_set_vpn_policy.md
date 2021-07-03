# 網路攻擊很猖狂

最近的網路攻擊超級猖狂的，因為工作的關係，其實我已經具備一定的危機意識了。所以家裡原本就有一台 FortiGate 60D 擋在最前面，做一個基本的防護。在過去也沒什麼特別設定，但是最近 [Qnap](https://www.qnap.com/zh-tw/security-news/2021/%E5%9B%9E%E6%87%89-qlocker-%E5%8B%92%E7%B4%A2%E7%97%85%E6%AF%92%E6%94%BB%E6%93%8A%E4%BA%8B%E4%BB%B6%E7%AB%8B%E5%8D%B3%E6%8E%A1%E5%8F%96%E8%A1%8C%E5%8B%95%E4%BF%9D%E8%AD%B7-qnap-nas) 的設備一直被攻擊，好多人珍貴的檔案都被加密了。讓我不得不重新審視家裡的網路安全。

所以過去沒特別鎖定連入 VPN 的位置與 IP ，在這次特別設定。也把 log 都打開，在第一時間就能收到警告。而[資料備份三二一原則](https://alanmoment.gitbook.io/moment/life/photo_backup_solution)也是基本的

## FortiGate 設定 VPN policy 之後沒有反應

在 GUI 介面設定完畢之後發現根本沒有作用，上網查了一下才發現...

```txt
Hi, you cannot block IPSec VPN traffic destined to the Fortigate IP itself with usual Security Rules - they only manage traffic PASSING the Fortigate from one interface to another.
To achieve that you need to use Local-in policy (viewable in GUI but editable in CLI only).
So your policy would look like (this will block ALL access from Ban_IP (only) to Fortigate, IPsec VPN, SSL VPN, Admin GUi etc. If you want to block just IPsec, set service accordingly):
```

> REF: https://forum.fortinet.com/tm.aspx?m=188611

因為 VPN 的 Policy 沒有辦法透過 GUI 介面操作，必須要透過指令修改才會生效。

下面就是這次加上的 Policy

### BAN 掉一些故意來試的

```conf
config firewall local-in-policy
  edit 1
  set intf "wan1"
  set srcaddr "Ban_IP"
  set dstaddr "all"
  set service "ALL"
  set schedule "always"
  set action deny
  set status enable
  next
end
```

### 直接鎖定只有台灣的 IP 才可以用 VPN

```conf
config firewall local-in-policy
  edit 2
  set intf "wan1"
  set srcaddr "ONLY_TAIWAN"
  set dstaddr "all"
  set service "ALL"
  set schedule "always"
  set action accept
  set status enable
  next
end
```

### 錯誤嘗試鎖定機制

```conf
config vpn ssl settings
  set login-attempt-limit 3          <----- Replace number of attempt to allow in place of x.
  set login-block-time 3600             <----- Replace number of seconds to block attempt in place of y.
end
```
