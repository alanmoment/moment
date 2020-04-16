# CentOS 安装 Node.js 0.8.5

很久很久以前，開發過社交網站，當時以Facebook與Plurk當作目標(瘋了)，但是最後還是以失敗收場。

那時候研究過Facebook與Plurk的留言機制，一直苦無方案可以解決"即時"的問題...也猜不透他們到底是怎麼做到的，一定有人說明明就很多方法，但那是四年前吶...

當時還試圖用Ajax來解決，發現!!可以耶。開心之餘的測試下。第二個人再來請求就死翹翹了...因為用timer會使的請求網頁一直處於等待回應的狀態。

於是呢...又重頭來開始找方法，最後皇天不負苦心人(已經快要爆肝)發現了[Node.js](http://nodejs.org/)它有worker的機制，當時他還在0.2.2的版本呢!有夠古老。所以現在來回味一下!!

### 安装Node.js前置作業

Node.js 0.8.5的安裝，需要python 2.7，大部分安装失敗，都是因為python版本過低。
 
	$ python -V
	Python 2.4.3

Node.js 0.8.5依賴的library

	$ yum install -y bzip2*

開始安裝python

	$ wget http://www.python.org/ftp/python/2.7.3/Python-2.7.3.tgz
	$ tar zvxf Python-2.7.3.tgz
	$ cd Python-2.7.3
	$ ./configure
	$ make && make install

因為系統預設是指向2.4.3，所以要重新建立連接，但是yum是在python 2.4.3才可以正常使用，所以不要隨便移除。

	$ mv /usr/bin/python  /usr/bin/python.bak
	$ ln -s /usr/local/bin/python2.7 /usr/bin/python

檢查python指向是否成功

	$ python -V   
	Python 2.7.3

### 安裝Node.js

可以到官網選擇合適的版本[下載](http://nodejs.org/download/)也可以用[github](https://github.com/joyent/node)上的版本。

	$ wget 'http://nodejs.org/dist/v0.8.5/node-v0.8.5.tar.gz'
	$ tar zvxf node-v0.8.5.tar.gz
	$ cd node-v0.8.5./configure
	$ make && make install

安裝上實在是很簡單，而且不知道為什麼我這次重新體驗安裝Node.js 0.8.5版本沒遇到什麼問題，以前安裝的時候嚐到不少苦頭，但現在要我回想有什麼苦頭我也忘了，總之一切正常就好。

### 安裝NVM

	$ git clone git://github.com/creationix/nvm.git ~/.nvm
	$ source ~/.nvm/nvm.sh

安裝並選擇版本。

	$ nvm install 0.10
	$ nvm use 0.10

查看你有什麼版本

	$ nvm ls

這樣就完成了

### HelloWorld

不免俗的要來寫一段世界級的程式碼HelloWorld看Node.js是否可以正常運作。

	$ mkdir /var/www/nodejs
	$ cd /var/www/nodejs
	$ vim HelloWorld.js
	var http = require('http');
	
	http.createServer(function (req, res) {
	  res.writeHead(200, {
	    'Content-Type': 'text/plain'
	  });
	  res.end('Hello Node.js');}).listen(8124, "127.0.0.1");
	
	console.log('Server running at http://127.0.0.1:8124/');

接下來啟動這一隻js

	$ node HelloWorld.js
	Server running at http://127.0.0.1:8124/

現在你可以打開瀏覽器輸入 http://127.0.0.1:8124 這時就可以在command-line看到Hello Node.js的訊息了。

參考：

[https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager)

> Jul 10th, 2013 5:04:00pm

> nodejs