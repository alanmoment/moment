# PHP 安裝 JSON

[JSON](http://www.json.org/)是一種資料格式，很多語言為了能互相溝通，都採用這種格式來傳遞資料，要讓 PHP 支援 JSON，需要另外安裝模組。

### 下載原始檔案包

```bash
cd /tmp
wget 'http://www.aurore.net/projects/php-json/php-json-ext-1.2.0.tar.bz2'
```

### 解壓縮

```bash
tar xvjf php-json-ext-1.2.0.tar.bz2
```

### 初始化 PHP 環境

```bash
cd php-json-ext-1.2.0
phpize
```

若出現錯誤表示沒有安裝過 phpize

```bash
phpize commend not found
```

這個可以在 yum 中安裝

```bash
yum -y install php-devel
```

如果還不行，說明你的編譯工具有問題，安裝一下就可以了

```bash
yum -y install gcc install autoconf automake libtool
```

再次測試運行 phpize 並重新安裝

```bash
./configure
make && makeinstall
```

### 檢查 JSON 是否安裝成功

```bash
find / -name ‘*json.so’
./usr/lib/php/modules/json.so
```

有出現路徑代表安裝成功

### 修改 php.ini

我的是在 php.ini 中 include 一個資料夾/etc/php.d

在這個檔中添一個 json.ini

```bash
vim json.ini
```

```ini
extension=json.so
```

### 重啟服務

```bash
$ service httpd restart
```

### 檢查 JSON 模組是否有載入

```bash
php -r "phpinfo();" | grep 'json'
json
json support => enabled
json version => 1.2.1
```

這樣就完成囉!!!

> Jul 17th, 2013 12:14:00am
