# Phalcon首發

用了好一陣子的[YII framework](http://www.yiiframework.com/)一開始用的時候覺得。哇~好專業阿。好難懂他的整個框架，摸了一陣子之後。才發現，近年不管哪一個程式語言。都是這樣的Design pattern。MVC、ORM，建立一個網站越來越便利也更簡單了。而最近發現一個類似YII的加強版[Phalcon](http://phalconphp.com/index)。主要他是用C
寫的，所以效能會比一般的framework還要好，但實際上我並沒有測試過，這只是我看官網上的介紹，而且我用framework，其實並不是那麼著墨在校能，而是提供的工具是否好用，開發上是否便利。如此而已。

### 下載Phalcon

官網有提供[Phalcon framework](http://docs.phalconphp.com/en/1.0.0/reference/install.html#linux-solaris-mac)的Github可以下載

	$ git clone git://github.com/phalcon/cphalcon.git
	$ cd cphalcon/build
	$ ./install

安裝畫面

![./install](https://lh3.googleusercontent.com/-GO7U-tfYzBE/UcEyQzo5l4I/AAAAAAAAAA8/_xJl0OvlNK4/w474-h209-no/cphalcon.PNG)

### 安裝便利的Phalcon Developer Tools

Phalcon一樣有提供[Phalcon Developer Tools](http://docs.phalconphp.com/en/latest/reference/linuxtools.html)的Github下載
	
	$ git clone https://github.com/phalcon/phalcon-devtools.git
	$ cd phalcon-devtools
	$ ./phalcon.sh

建立softlink

	$ ln -s /home/phalcon/phalcon-devtools/phalcon.php /usr/bin/phalcon
	$ chmod ugo+x /usr/bin/phalcon

安裝完畢後重新登入執行測試

	$ phalcon commands

執行畫面

![phalcon commands](https://lh4.googleusercontent.com/-_Py905A8R7o/UcEyQ7LRWYI/AAAAAAAAABA/01i1S35xEsY/w373-h219-no/cphalcon-devtools.PNG)

### 建立專案

使用指令建立一個專案

	$ phalcon create-project phalcon

![phalcon create-project phalcon](https://lh5.googleusercontent.com/-EB4pamSa3CA/UcEyQ7MKXVI/AAAAAAAAAA4/NgM7_Mz4hjM/w477-h168-no/cphalcon-create-project.PNG)

這樣就完成了。在我的[小專案](http://demo.ocomm.com.tw/hadoop)中也有用到這個framework。

> Aug 28th, 2013 11:54:00am