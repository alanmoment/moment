# Baby Help on Tessel

# 發想

突然想到小朋友在嬰兒時期，常常一個人在房間睡覺，不曉得什麼時候會哭。所以市面上有產品是偵測到嬰兒的哭聲會嗡嗡的叫，套用在 Tessel，我就想到我也可以實現了。

- Tessel
- [Ambient Module][1]
- [Camera Module][2]

![](/assets/IMG_3953.JPG)

# 聲音偵測

用來偵測嬰兒哭的時候要觸發的事件，還沒有調整範圍。

	# index.js

在這邊會先跟 wifi 連線。

	# wifi-control.js

# 照片上傳 Slack

只要偵測到異常，就會觸發照相，然後上傳到 Slack，一整個超愛用 Slack...。相關檔案上傳可以參考[文件][3]，只需要申請一組 [Token][4] 就可以傳到指定的 `channels`，吃了不少苦頭，主要是不熟悉 NodeJS，不過好險最後有找到[參考][5]的。

	# send_notify/index.js

完成！！好開心啊！

	$ tessel run index.js --upload-dir ./
	TESSEL! Connected to TM-00-04-f000da30-00544741-1ca82586.
	INFO Bundling directory /Users/alan/SourceTree/baby-help
	INFO Deploying bundle (530.00 KB)...
	INFO Running script...
	System will be start...
	Light level: 0.01464844   Sound Level: 0.01855469
	Light level: 0.01464844   Sound Level: 0.01855469
	Light level: 0.01562500   Sound Level: 0.01855469
	Light level: 0.01562500   Sound Level: 0.01855469
	connect emitted {
	 dns : 168.95.192.1,
	 ip : 192.168.1.46,
	 event : status,
	 connected : 1,
	 dhcp : 192.168.1.1,
	 ssid : Alan 的 AirPort Time Capsule,
	 gateway : 192.168.1.1
	}
	Light level: 0.01464844   Sound Level: 0.01855469
	Light level: 0.01464844   Sound Level: 0.01855469
	Light level: 0.01562500   Sound Level: 0.01855469
	Light level: 0.01562500   Sound Level: 0.01855469
	Light level: 0.01464844   Sound Level: 0.01855469
	Light level: 0.01562500   Sound Level: 0.01855469
	Light level: 0.01464844   Sound Level: 0.01855469
	Light level: 0.01562500   Sound Level: 0.02050781
	Something happened with sound:  0.1005859375
	Light level: 0.01562500   Sound Level: 0.01855469
	Light level: 0.01562500   Sound Level: 0.01855469
	Light level: 0.01464844   Sound Level: 0.01855469

只是畫素很差...

![](/assets/baby-help.png)

程式碼都放到 [Github][6] 了。

[1]: https://tessel.io/modules#module-ambient
[2]: https://tessel.io/modules#module-camera
[3]: https://api.slack.com/methods/files.upload
[4]: https://api.slack.com/web
[5]: https://projects.tessel.io/projects/tesselcam
[6]: https://github.com/alanmoment/baby-help

> May 11th, 2015 1:06:12am