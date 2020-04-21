# Connect to Slack on Tessel

## 申請 Slack

因為 [Slack][1] 非常方便且好用，所以我第一個想到要串接的對象就是它，接連幾天的測試，已經能透過 Tessel 發送訊息至 Slack Channel。

首先得先擁有一組 Slack 的帳號與 Channel，再設置 Incoming WebHooks。

![](/assets/incoming-webhooks.png)

便可取得 Webhook Url

![](/assets/webhooks-url.png)

## Tessel Wifi

先讓 Tessel 連接家中的 Wifi

	$ tessel wifi -n 'Alan 的 AirPort Time Capsule' -p 'PASSWORD'
	TESSEL! Connected to TM-00-04-f000da30-00544741-1ca82586.
	INFO Connecting to "Alan 的 AirPort Time Capsule" with wpa2 security...
	INFO Acquiring IP address. 
	..
	INFO Connected!
	
	IP	 192.168.1.46
	DNS	 168.95.192.1
	DHCP	 192.168.1.1
	Gateway	 192.168.1.1

依照 Tessel 在官網上 [Wifi][2] 的範例，修改一下程式碼：

	# wifi-control.js
	
	var https = require('https');
	
	...
	...
	wifi.on('connect', function(data) {

	    console.log("connect emitted", data);
	
	    var json = JSON.parse('{"text":"This is a line of text in a channel.\nAnd this is another line of text."}');
	    var postData = 'payload=' + JSON.stringify(json);
	
	    var options = {
	        hostname: 'hooks.slack.com',
	        port: 443,
	        path: '/services/T030MA704/B04MARY2F/clicTENx48f4h5v9OePzePkH',
	        method: 'POST',
	        headers: {
	            'Content-Type': 'application/x-www-form-urlencoded',
	            'Content-Length': postData.length
	        }
	    };
	
	    // Setup the request.  The options parameter is
	    // the object we defined above.
	    var req = https.request(options, function(res) {
	        res.setEncoding('utf-8');
	        var responseString = '';

	        res.on('data', function(data) {
	            responseString += data;
	        });
	
	        res.on('end', function() {
	            console.log(responseString);
	        });
	    });
	
	    req.on('error', function(e) {
	        console.log('something error.');
	    });
	
	    req.write(postData);
	    req.end();
	
	}
	...
	...
	
因為 Slack 是透過 `https` 發送，所以這邊得使用 `https.request` 來傳遞封包。
	
運行 Wifi-control.js 測試是否可以正常傳送至 Slack Channel。
	
	$ tessel run wifi-control.js
	TESSEL! Connected to TM-00-04-f000da30-00544741-1ca82586.
	INFO Bundling directory /Users/alan/Documents/tessel-code
	INFO Deploying bundle (8.87 MB)...
	INFO Running script...
	connect emitted {
	 dns : 168.95.192.1,
	 ip : 192.168.1.46,
	 event : status,
	 connected : 1,
	 dhcp : 192.168.1.1,
	 ssid : Alan 的 AirPort Time Capsule,
	 gateway : 192.168.1.1
	}
	ok

測試可以正常收到囉！！

![](/assets/wifi-test-on-slack.png)

如此就可以應用在各種需要通知的狀況上，太棒了！

## Wifi 斷線

要如何讓 Tessel 斷開 wifi 呢，只要下這行指令：

	$ tessel wifi -d
	TESSEL! Connected to TM-00-04-f000da30-00544741-1ca82586.
	Erasing saved wifi profiles
	Erased wifi profiles

太方便了...

[1]: https://slack.com
[2]: http://start.tessel.io/wifi

> May 3rd, 2015 1:41:16am