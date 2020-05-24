# PHP安裝JSON

[JSON](http://www.json.org/)是一種資料格式，很多語言為了能互相溝通，都採用這種格式來傳遞資料，要讓PHP支援JSON，需要另外安裝模組。

### 下載原始檔案包

```
$ cd /tmp
$ wget 'http://www.aurore.net/projects/php-json/php-json-ext-1.2.0.tar.bz2'
```

### 解壓縮

```
$ tar xvjf php-json-ext-1.2.0.tar.bz2
```

### 初始化PHP環境

```	
$ cd php-json-ext-1.2.0
$ phpize
```

若出現錯誤表示沒有安裝過phpize

```
phpize commend not found
```

這個可以在yum中安裝

```
$ yum -y install php-devel
```

如果還不行，說明你的編譯工具有問題，安裝一下就可以了

```
$ yum -y install gcc install autoconf automake libtool
```

再次測試運行phpize並重新安裝

```
$ ./configure
$ make && makeinstall
```

### 檢查JSON是否安裝成功

```
$ find / -name ‘*json.so’
./usr/lib/php/modules/json.so
```

有出現路徑代表安裝成功

### 修改php.ini

我的是在php.ini中include一個資料夾/etc/php.d

在這個檔中添一個json.ini

```
$ vim json.ini
extension=json.so
```

### 重啟服務

```
$ service httpd restart
```

### 檢查JSON模組是否有載入

```
$ php -r "phpinfo();" | grep 'json'
$ json
$ json support => enabled
$ json version => 1.2.1
```

這樣就完成囉!!!

> Jul 17th, 2013 12:14:00am