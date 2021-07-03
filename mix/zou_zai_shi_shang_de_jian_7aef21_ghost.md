# 走在時尚的尖端! Ghost

今天我也要來趕流行一下，最近很流行[Ghost](https://en.ghost.org/)這東東，這東東是甚麼呢？他有點像[Octopress](https://http://octopress.org/)，但是卻不像 Octopress 比較困難架。

Ghost 是架構在[Nodejs](http://nodejs.org/)上的，可以用極短..極短..極短的時間架好 blog。一分鐘就可以架好了！！就是這麼簡單，所以也沒有甚麼流程，只要安裝 Nodejs 基本上就完成了。

Ghost 允許線上用[Markdown](http://markdown.tw/)編輯文章，不像 Octopress 得依靠 Command line 或工具。

## Install Nodejs

可以按照官網上的[Install Node](http://docs.ghost.org/installation/linux/)步驟安裝，或是[CentOS 安装 Node.js 0.8.5](/nodejs/centos_an_zhuang_node__js_0__8__5.md)也可以看一下。

## Install Ghost

註冊 Ghost 就可以到 Download 畫面下載。

接著五個步驟就完成了。

```bash
wget 'https://en.ghost.org/zip/ghost-0.3.2.zip'
unzip ghost-0.3.2.zip -d ghost
cd ghost
npm install --production
npm start
```

這樣就可以 run 囉!!簡單到炸掉阿...

![build ghost](/assets/mix/zou_zai_shi_shang_de_jian_7aef21_ghost/build_ghost.png)

畫面非常乾淨漂亮。

## 後台管理

輸入網址`http://your-domain/ghost`就可以註冊一個 admin 的帳號。

![admin sign](/assets/mix/zou_zai_shi_shang_de_jian_7aef21_ghost/admin_sign.png)

## 設定 config.js

網站目錄底下有個 config.js，若沒有則自己複製一份。

```bash
cp -a config.example.js config.js
```

這邊將全部的`my-ghost-blog.com`替換成自己的 domain。

## PM2 管理 Ghost

若是使用`npm start`啟動，會有點難管理，而且 nodejs 最為人詬病的就是不 friendly。所以這邊我是使用[PM2](https://github.com/Unitech/pm2)。Github 上有很詳細的安裝方式，就不再說明。應用在 Ghost 則可以執行。

```bash
pm2 start ghost/index.js
```

看執行中的程式

```bash
pm2 list
```

看程式負載

```bash
pm2 monit
```

也可以看 log

```bash
pm2 logs
```

> Oct 24th, 2013 10:34:00am
