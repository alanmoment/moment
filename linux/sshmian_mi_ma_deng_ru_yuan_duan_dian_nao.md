# SSH 免密碼登入遠端電腦

YUM 安裝 SSH

```bash
yum -y install openssh
```

用 ssh-keygen 做出公用和私有鑰匙，並且設置無密碼連接，把 master 的 id_rsa.pub 傳給 slave

```bash
$ ssh-keygen -t rsa -C root
Generating public/private dsa key pair.
Enter file in which to save the key (/home/root/.ssh/id_rsa):
Enter passphrase (empty for no passphrase): > /root/.ssh/authorized_keys
$ exit
```

再登入一次 slave

```bash
ssh 192.168.1.29
```

已經不用再輸入密碼囉，這樣就完成了。

**2014/1/15 9:37 pm 將 id_dsa 修正為 id_rsa**

> Jun 16th, 2013 10:37:00pm
