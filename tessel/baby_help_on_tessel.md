# Baby Help on Tessel

突然想到小朋友在嬰兒時期，常常一個人在房間睡覺，不曉得什麼時候會哭。所以市面上有產品是偵測到嬰兒的哭聲會嗡嗡的叫，套用在 Tessel，我就想到我也可以實現了。

- Tessel
- [Ambient Module](https://tessel.io/modules#module-ambient)
- [Camera Module](https://tessel.io/modules#module-camera)

![](/assets/tessel/baby_help_on_tessel/IMG_3953.JPG)

## 聲音偵測

用來偵測嬰兒哭的時候要觸發的事件，還沒有調整範圍。

index.js

```js
// Any copyright is dedicated to the Public Domain.
// http://creativecommons.org/publicdomain/zero/1.0/

/*********************************************
This ambient module example console.logs
ambient light and sound levels and whenever a
specified light or sound level trigger is met.
*********************************************/

var fs = require("fs");
var https = require("https");
var tessel = require("tessel");

var ambientlib = require("ambient-attx4");
var ambient = ambientlib.use(tessel.port["A"]);

var camera = require("camera-vc0706").use(tessel.port["B"]);
var notificationLED = tessel.led[1]; // Set up an LED to notify when we're taking a picture

var my_wifi = require("./wifi-control");
var send_notify = require("./send_notify");

ambient.on("ready", function () {
  console.log("System will be start...");
  notificationLED.high();

  // Get points of light and sound data.
  setInterval(function () {
    ambient.getLightLevel(function (err, ldata) {
      if (err) throw err;
      ambient.getSoundLevel(function (err, sdata) {
        if (err) throw err;
        console.log(
          "Light level:",
          ldata.toFixed(8),
          " ",
          "Sound Level:",
          sdata.toFixed(8)
        );
      });
    });
  }, 500); // The readings will happen every .5 seconds unless the trigger is hit

  ambient.setLightTrigger(0.5);

  // Set a light level trigger
  // The trigger is a float between 0 and 1
  ambient.on("light-trigger", function (data) {
    console.log("Our light trigger was hit:", data);

    // Clear the trigger so it stops firing
    ambient.clearLightTrigger();
    //After 1.5 seconds reset light trigger
    setTimeout(function () {
      ambient.setLightTrigger(0.5);
    }, 1500);
  });

  // Set a sound level trigger
  // The trigger is a float between 0 and 1
  ambient.setSoundTrigger(0.2);

  ambient.on("sound-trigger", function (data) {
    console.log("Something happened with sound: ", data);

    // Clear it
    ambient.clearSoundTrigger();

    //After 1.5 seconds reset sound trigger
    setTimeout(function () {
      ambient.setSoundTrigger(0.2);

      // Take the picture
      camera.takePicture(function (err, image) {
        if (err) {
          console.log("error taking image", err);
        } else {
          notificationLED.low();
          var filename = "picture-" + Math.floor(Date.now() * 1000) + ".jpg";
          console.log("Picture as", filename, "...");
          // saved picture
          // process.sendfile(filename, image);

          // var image = my_camera.picture();
          var send = new send_notify("Baby is crying...", filename, image);
          send.post();

          notificationLED.high();

          sleep(5);
        }
      });
    }, 1500);
  });
});

ambient.on("error", function (err) {
  console.log(err);
});

function sleep(sec) {
  sec = sec * 1000;
  var start = new Date().getTime();
  while (true) {
    if (new Date().getTime() - start > sec) {
      break;
    }
  }
}
```

在這邊會先跟 wifi 連線。

wifi-control.js

```js
/* the wifi-cc3000 library is bundled in with Tessel's firmware,
 * so there's no need for an npm install. It's similar
 * to how require('tessel') works.
 */
var https = require("https");
var wifi = require("wifi-cc3000");
var network = "Alan 的 AirPort Time Capsule"; // put in your network name here
var pass = "XXXXXXXX"; // put in your password here, or leave blank for unsecured
var security = "wpa2"; // other options are 'wep', 'wpa', or 'unsecured'
var timeouts = 0;

function connect() {
  wifi.connect({
    security: security,
    ssid: network,
    password: pass,
    timeout: 30, // in seconds
  });
}

wifi.on("connect", function (data) {
  console.log("connect emitted", data);
});

wifi.on("disconnect", function (data) {
  // wifi dropped, probably want to call connect() again
  console.log("disconnect emitted", data);
});

wifi.on("timeout", function (err) {
  // tried to connect but couldn't, retry
  console.log("timeout emitted");
  timeouts++;
  if (timeouts > 2) {
    // reset the wifi chip if we've timed out too many times
    powerCycle();
  } else {
    // try to reconnect
    connect();
  }
});

wifi.on("error", function (err) {
  // one of the following happened
  // 1. tried to disconnect while not connected
  // 2. tried to disconnect while in the middle of trying to connect
  // 3. tried to initialize a connection without first waiting for a timeout or a disconnect
  console.log("error emitted", err);
});

// reset the wifi chip progammatically
function powerCycle() {
  // when the wifi chip resets, it will automatically try to reconnect
  // to the last saved network
  wifi.reset(function () {
    timeouts = 0; // reset timeouts
    console.log("done power cycling");
    // give it some time to auto reconnect
    setTimeout(function () {
      if (!wifi.isConnected()) {
        // try to reconnect
        connect();
      }
    }, 20 * 1000); // 20 second wait
  });
}
```

## 照片上傳 Slack

只要偵測到異常，就會觸發照相，然後上傳到 Slack，一整個超愛用 Slack...。相關檔案上傳可以參考[文件](https://api.slack.com/methods/files.upload)，只需要申請一組 [Token](https://api.slack.com/web) 就可以傳到指定的 `channels`，吃了不少苦頭，主要是不熟悉 NodeJS，不過好險最後有找到[參考](https://projects.tessel.io/projects/tesselcam)的。

完成！！好開心啊！

```bash
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
```

只是畫素很差...

![](/assets/tessel/baby_help_on_tessel/baby-help.png)

程式碼都放到 [Github](https://github.com/alanmoment/baby-help) 了。

> May 11th, 2015 1:06:12am
