# SSH免密碼登入遠端電腦

YUM安裝SSH

	$ yum -y install openssh

用ssh-keygen做出公用和私有鑰匙，並且設置無密碼連接，把master的id\_rsa.pub傳給slave

	
	$ ssh-keygen -t rsa -C root
	Generating public/private dsa key pair.
	Enter file in which to save the key (/home/root/.ssh/id_rsa):
	Enter passphrase (empty for no passphrase): > /root/.ssh/authorized_keys
	$ exit

再登入一次slave

	$ ssh 192.168.1.29

已經不用再輸入密碼囉，這樣就完成了。

> 2014/1/15 9:37 pm 將id_dsa修正為id_rsa

> Jun 16th, 2013 10:37:00pm

> centos, ssh