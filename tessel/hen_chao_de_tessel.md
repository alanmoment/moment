# 很潮的 Tessel

# Tessel

![Tessel](/assets/tessel/hen_chao_de_tessel/IMG_2305.JPG)

這個夢寐以求的版子，終於狠下心進口了！只是這小玩意兒的身世有點可憐，竟然被郵差送錯地方...而那個人也很奇怪，東西不是自己的也收下來了！還拆封！！！到底是為什麼敢拆不是自己的東西！！驗收到凌晨4點...好險東西都正常。

先下載 Tessel lib 

	$ npm install -g tessel

要更新 Tessel lib 否則會無法執行

	$ tessel update
	TESSEL! Connected to TM-00-04-f000da30-00544741-1ca82586.
	INFO Checking for latest firmware... 
	INFO Wifi version is also outdated.
	INFO Downloading remote file https://builds.tessel.io/wifi/1.28.bin
	INFO Wifi patch uploaded... waiting for it to apply (10s)
	INFO ...
	INFO ...
	INFO ...
	INFO ...
	INFO ...
	INFO ...
	INFO 
	INFO Downloading remote file https://builds.tessel.io/firmware/tessel-firmware-current.bin
	INFO Updating firmware... please wait. Tessel will reset itself after the update
	INFO Complete  1175988 /1175988
	
建立一個官方的範例測試

	// blinky.js
	
	// Import the interface to Tessel hardware
	var tessel = require('tessel');
	
	// Set the led pins as outputs with initial states
	// Truthy initial state sets the pin high
	// Falsy sets it low.
	var led1 = tessel.led[0].output(1);
	var led2 = tessel.led[1].output(0);
	
	setInterval(function () {
	    console.log("I'm blinking! (Press CTRL + C to stop)");
	    // Toggle the led states
	    led1.toggle();
	    led2.toggle();
	}, 100);
	
OK，執行成功就會看到藍綠兩個燈在閃爍！

	$ tessel run blinky.js 
	TESSEL! Connected to TM-00-04-f000da30-00544741-1ca82586.
	INFO Bundling directory /Users/alan/Documents/tessel-code
	INFO Deploying bundle (4.50 KB)...
	INFO Running script...
	I'm blinking! (Press CTRL + C to stop)
	
![example](/assets/tessel/hen_chao_de_tessel/IMG_2308.JPG)

Tessel 真的不錯，除了程式語言可以用比較熟悉的 Javascript，Module 也用`npm`管理，很順暢。整個在水準之上，看得出來團隊很用心的在開發，而我也預購了 Tessel 2 的新版子，因為我覺得非常棒。

> Apr 18th, 2015 3:49:59am