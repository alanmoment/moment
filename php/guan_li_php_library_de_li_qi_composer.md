# 管理 PHP Library 的利器 Composer

在之前試玩[Ruby On Rails](http://rubyonrails.org/)的時候就覺得有[Gem](http://rubygems.org/)一整個就是超好用的吶!!所以前陣子公司導入 Gitlab 之後順勢要我研究一下 PHP 的[Composer](http://getcomposer.org/)，想不到還有支援 Github。所以做個紀錄。

### 安裝 PHP Composer

安裝在各自專案時

```bash
mkdir /var/www/html/example
sudo curl -s https://getcomposer.org/installer | sudo php
```

![install composer](https://lh4.googleusercontent.com/-1d9vXwf_95M/Uchd7udmlMI/AAAAAAAAADY/w8qUK7xXyFY/w850-h263-no/composer-1.PNG)

> 我安裝時有出現警告訊息是因為我的 PHP 版本是 5.3.3 他建議我升級到 5.3.4 或更高，否則可能導致 Composer 不穩定

我自己是安裝在/usr/local/bin

```bash
cd /usr/local/bin
sudo curl -s https://getcomposer.org/installer | sudo php
```

可建立指向

```bash
ln -s /usr/local/bin/composer.phar /usr/bin/composer
```

這樣不管在哪個專案只要建立 composer.json 都可以使用了。

### 在 PHP 專案建立 Composer

建立 composer.json

```bash
cd /var/www/html/example
```

composer.json

```json
{
  "require": {
    "monolog/monolog": "1.2.*"
  }
}
```

建立後執行

```bash
composer install
```

專案目錄底下就會產生 vendor 資料夾，裏頭就是在 composer.json 有設定要引入的 library。

Composer 官網也有提供線上的[Library](https://packagist.org/)可以使用。讚!!

### 使用 Github 上的 Repository

把 library 放到 github 上管理，然後再用 composer 引入各自的專案，這豈不是太方便了!!

```bash
vim composer.json
```

```json
{
  "name": "bom-solution",
  "description": "bom solution",
  "homepage": "https://github.com/alanmoment/bom-solution",
  "repositories": [
    {
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
    }
  ],
  "require": {
    "php": ">=5.3.3",
    "alanmoment/bom-solution": "v0.1"
  }
}
```

> 建立 github 的 composer 之前必須先在你的 repository 建立 tag
> alanmoment 是我的 github 的帳號
> bom-solution 是我的 repository
> v0.1 是我 tag 的版號

存檔後執行

```bash
composer install
```

會發生以下錯誤

```txt
Loading composer repositories with package information
Installing dependencies (including require-dev) from lock file
Nothing to install or update
Generating autoload files
```

這是因為執行過 composer install 之後目錄底下也會產生一隻 composer.lock 檔案，要再次執行前先把他刪掉，就可以正常執行囉
。

![composer install](https://lh5.googleusercontent.com/-2iiSaEx-0A8/Uchd7p8nS7I/AAAAAAAAADc/qlzt3gnb9tI/w645-h182-no/composer.PNG)

完成囉!!

> Jul 4th, 2013 12:36:00am
