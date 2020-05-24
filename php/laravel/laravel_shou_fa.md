# Laravel 首發 !!!

雖然[Laravel][1]已經出現頗久，最近總算開始動動手了，有鑑於去年開始使用Ruby on Rails，發現說Laravel也太像了吧。據匿名的朋友說現在的Framework幾乎都是`參考`Ruby on Rails。而這次也趁機再多用幾種工具，

- [Nitrous.io][2]
- AWS S3
- [Heroku][3]

每次自己開專案都想用不同的工具，而現在的工具越來越簡單，若是不瞭解原理，那豈不是很快被淘汰了嗎? 

## 建立Laravel

現在的環境，撰寫如何建立似乎已經不怎麼高手了，因為越來越簡單，而[官網][4]寫的也很好。所以就不再Copy了，倒是因為要使用Framework有遇到一些`PHP module`的問題。

就以前的開發環境都會有[php-mcrypt][5]這個模組，但這次使用的環境竟然就真的沒有，或許是版本過新、或是MIS沒有安裝。不管，反正遇到問題就是要解決了

在CentOS 6.7中，使用yum找不到php-mcrypt了，必須要安裝另外的套件尋找。

```
$ sudo rpm -ivh http://dl.fedoraproject.org/pub/epel/beta/7/x86_64/epel-release-7-0.2.noarch.rpm
$ sudo rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
$ sudo yum --enablerepo=remi install php-mcrypt.x86_64
$ sudo service httpd restart
```

`php -m`就可以看到`mcrypt`模組了。

## 使用Laravel

使用起來真的是很順手，因為幾乎跟Ruby on Rails一樣了，但畢竟不是很熟手，只記錄有用到的。

### Routes

進入網站的各個位址都會經過route。

/secret直接401

```
Route::any('/secret', function() {
    return Response::make('Unauthorized', 401);
});
```

/form只接受post並且在之前會先檢查ip

```
Route::match(
    array('POST'), '/form', array('before' => 'ipcheck', 'uses' => 'HomeController@purchase')
);
```

### Filters

是用來過濾各種請求，有App層級也有Controller的，可用下例程式碼過濾IP。

```
Route::filter('ipcheck', function() {
    if (!preg_match("/127\.0\.0\.1/i", $_SERVER['REMOTE_ADDR']) {
        return Redirect::to('/');
    }
});
```

### App

app/config/app.php

- debug => 是否開啓除錯
- url => 網站預設網址

透過一些static method也能存取其中屬性。

```
Config::set('app.url', STATIC_HOST);
Config::get('app.url');
```

### Database

Database當然也有好用的ORM，而我覺得好用的程度跟Ruby有得比唷。

app/config/database.php

- default => 預設的資料庫
- connections => 資料庫連線位址、帳號、密碼...等
- redis => 好用的快取

透過attribute可以改變連線的資料庫、使用資料表、欄位是否使用...等。

```
class User extends Eloquent {

    protected $connection = DATABASE;

    protected $table = TABLE;
    
    public $timestamps = false;
    
    public function userinfo() {
        return $this->hasOne('Userinfo');
    }

}

class Userinfo extends Eloquent {

    protected $connection = DATABASE;

    protected $table = 'user_info';
    
    public function user() {
        return $this->belongsTo('User');
    }

}
```

當資料表有關聯，可以透過function設定關係，上例關係是User有一個Userinfo，而Userinfo也相依於User，如此在使用時可利用下例程式碼存取到userinfo的資料，

```
User::with('userinfo')->find(1)->first();
```

若是沒有設定，也可以利用下利存取，但似乎就比較沒有彈性。

```
User::leftJoin('user_info as info')->find(1).first();
```

累了...連laravel都沒紀錄完....下回再打...

[1]: http://laravel.com/
[2]: https://www.nitrous.io
[3]: https://www.heroku.com/
[4]: http://laravel.com/docs/quick
[5]: http://php.net/manual/en/book.mcrypt.php

> Aug 19th, 2014 11:11:00am