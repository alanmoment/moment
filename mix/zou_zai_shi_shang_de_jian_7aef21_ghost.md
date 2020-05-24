# 走在時尚的尖端! Ghost

今天我也要來趕流行一下，最近很流行[Ghost][1]這東東，這東東是甚麼呢？他有點像[Octopress][2]，但是卻不像Octopress比較困難架。

Ghost是架構在[Nodejs][3]上的，可以用極短..極短..極短的時間架好blog。一分鐘就可以架好了！！就是這麼簡單，所以也沒有甚麼流程，只要安裝Nodejs基本上就完成了。

Ghost允許線上用[Markdown][5]編輯文章，不像Octopress得依靠Command line或工具。

## Install Nodejs

可以按照官網上的[Install Node][4]步驟安裝，或是[CentOS 安装 Node.js 0.8.5][6]也可以看一下。

## Install Ghost

註冊Ghost就可以到Download畫面下載。

接著五個步驟就完成了。

```
$ wget 'https://en.ghost.org/zip/ghost-0.3.2.zip'
$ unzip ghost-0.3.2.zip -d ghost
$ cd ghost
$ npm install --production
$ npm start
```

這樣就可以run囉!!簡單到炸掉阿...

![build ghost](/assets/mix/zou_zai_shi_shang_de_jian_7aef21_ghost/build_ghost.png)

畫面非常乾淨漂亮。

## 後台管理

輸入網址`http://your-domain/ghost`就可以註冊一個admin的帳號。

![admin sign](/assets/mix/zou_zai_shi_shang_de_jian_7aef21_ghost/admin_sign.png)

## 設定config.js

網站目錄底下有個config.js，若沒有則自己複製一份。

```
$ cp -a config.example.js config.js
```

這邊將全部的`my-ghost-blog.com`替換成自己的domain。

## PM2管理Ghost

若是使用`npm start`啟動，會有點難管理，而且nodejs最為人詬病的就是不friendly。所以這邊我是使用[PM2](7)。Github上有很詳細的安裝方式，就不再說明。應用在Ghost則可以執行。

```
$ pm2 start ghost/index.js
```

看執行中的程式

```
$ pm2 list
```

看程式負載

```
$ pm2 monit
```

也可以看log

```
$ pm2 logs
```

[1]: https://en.ghost.org/
[2]: https://http://octopress.org//
[3]: http://nodejs.org/
[4]: http://docs.ghost.org/installation/linux/
[5]: http://markdown.tw/
[6]: /post/94171044168/centos-node-js-0-8-5
[7]: https://github.com/Unitech/pm2

> Oct 24th, 2013 10:34:00am