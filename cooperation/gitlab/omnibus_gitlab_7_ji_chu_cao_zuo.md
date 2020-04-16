# Omnibus Gitlab 7 基礎操作

# Gitlab Manual

![Gitlab][1]

## Overview

Used [Omnibus][2]

> CentOS 6

**repository path**

/var/opt/gitlab/git-data/repositories

**backup path**

/var/opt/gitlab/backups

### Install

	$ curl -O https://downloads-packages.s3.amazonaws.com/centos-6.6/gitlab-7.5.3_omnibus5.2.1.ci-1.el6.x86_64.rpm
	$ sudo yum install openssh-server
	$ sudo yum install postfix
	$ sudo yum install cronie
	$ sudo service postfix start
	$ sudo chkconfig postfix on
	$ sudo rpm -i gitlab-7.5.3_omnibus.5.2.1.ci-1.el6.x86_64.rpm

Default account: root / 5iveL!fe

### Setup

**configure**

	$ sudo vim /etc/gitlab/gitlab.rb
	
	external_url 'http://gdd-gitlab.unalis.com.tw'
	
> reference [README.md][config README.md]

**reset**

	$ sudo gitlab-ctl reconfigure
	
### Performance Tuning

**worker proccess**

	$ sudo vim /var/opt/gitlab/gitlab-rails/etc/unicorn.rb
	
	# How many worker processes
	worker_processes 2
	
### Upgrade

**stop service**

	$ sudo gitlab-ctl stop unicorn
	$ sudo gitlab-ctl stop sidekiq
	$ sudo gitlab-ctl stop nginx
	
**backup**

	$ sudo gitlab-rake gitlab:backup:create
	
**upgrade version download**

Go to [download][2] page. Download `CentOS 6` version rpm package.

**execute**

	$ sudo rpm -Uvh gitlab-x.x.x_xxx.rpm
	
**configure reset**

	$ sudo gitlab-ctl reconfigure
	
**service restart**

	$ sudo gitlab-ctl restart
	
> reference [README.md][upgrade README.md]

### Backup

	$ sudo gitlab-rake gitlab:backup:create

### Something Error

#### upgrade

	$ gitlab-ctl reconfigure /opt/gitlab/embedded/bin/ruby: /lib64/libc.so.6: version `GLIBC_2.14' not found (required by /opt/gitlab/embedded/lib/libruby.so.2.1) /opt/gitlab/embedded/bin/ruby: /lib64/libc.so.6: version `GLIBC_2.17' not found (required by /opt/gitlab/embedded/lib/libruby.so.2.1)
	
Upgrade version not match install version.

[1]: http://www.bloggure.info/images/uploads/2012/02/gitlabhq-logo.png
[2]: https://about.gitlab.com/downloads/
[config README.md]: https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/README.md
[upgrade README.md]: https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/doc/update.md

> Dec 11th, 2014 2:35:00pm