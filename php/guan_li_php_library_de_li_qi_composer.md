# 管理PHP Library的利器Composer

在之前試玩[Ruby On Rails](http://rubyonrails.org/)的時候就覺得有[Gem](http://rubygems.org/)一整個就是超好用的吶!!所以前陣子公司導入Gitlab之後順勢要我研究一下PHP的[Composer](http://getcomposer.org/)，想不到還有支援Github。所以做個紀錄。

### 安裝PHP Composer

安裝在各自專案時

	$ mkdir /var/www/html/example
	$ sudo curl -s https://getcomposer.org/installer | sudo php

![install composer](https://lh4.googleusercontent.com/-1d9vXwf_95M/Uchd7udmlMI/AAAAAAAAADY/w8qUK7xXyFY/w850-h263-no/composer-1.PNG)

> 我安裝時有出現警告訊息是因為我的PHP版本是5.3.3他建議我升級到5.3.4或更高，否則可能導致Composer不穩定

我自己是安裝在/usr/local/bin

	$ cd /usr/local/bin
	$ sudo curl -s https://getcomposer.org/installer | sudo php

可建立指向

	$ ln -s /usr/local/bin/composer.phar /usr/bin/composer

這樣不管在哪個專案只要建立composer.json都可以使用了。

### 在PHP專案建立Composer

建立composer.json

	$ cd /var/www/html/example
	$ vim composer.json
	{
	    "require": {
	        "monolog/monolog": "1.2.*"
	    }
	}

建立後執行

	$ composer install

專案目錄底下就會產生vendor資料夾，裏頭就是在composer.json有設定要引入的library。

Composer官網也有提供線上的[Library](https://packagist.org/)可以使用。讚!!

### 使用Github上的Repository

把library放到github上管理，然後再用composer引入各自的專案，這豈不是太方便了!!

	$ vim composer.json
	{
	    "name": "bom-solution",
	    "description": "bom solution",
	    "homepage": "https://github.com/alanmoment/bom-solution",
	    "repositories": [{
	    "type": "package",
	    "package": {
	        "name": "alanmoment/bom-solution",
	        "version": "v0.1",
	        "source": {
	                "url": "git@github.com:alanmoment/bom-solution.git",
	                "type": "git",
	                "reference": "v0.1"
	        }
	        }
	    }],
	    "require": {
	        "php": ">=5.3.3",
	        "alanmoment/bom-solution": "v0.1"
	    }
	}

> 建立github的composer之前必須先在你的repository建立tag

> alanmoment是我的github的帳號

> bom-solution是我的repository

> v0.1是我tag的版號

存檔後執行

	$ composer install

會發生以下錯誤

	Loading composer repositories with package information
	Installing dependencies (including require-dev) from lock file
	Nothing to install or update
	Generating autoload files

這是因為執行過composer install之後目錄底下也會產生一隻composer.lock檔案，要再次執行前先把他刪掉，就可以正常執行囉
。

![composer install](https://lh5.googleusercontent.com/-2iiSaEx-0A8/Uchd7p8nS7I/AAAAAAAAADc/qlzt3gnb9tI/w645-h182-no/composer.PNG)

完成囉!!

> Jul 4th, 2013 12:36:00am

> php, git, composer