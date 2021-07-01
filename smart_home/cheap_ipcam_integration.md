# Fibaro 系統整合便宜的 IP Cam

在我的智慧家庭系統中，初期整合了兩台 D-Link 的 IP Cam，因為一開始怕規格不符整合不進去，所以只能挑官方推薦的型號，但實在又舊又貴

1. DCS-7010L

    號稱 IP66 防水防塵，確實是蠻耐用的，優點有 PoE，沒什麼不好，就是價格很貴
2. DCS-5222LB

    只是一台可以旋轉的 IP Cam

這兩台我都覺得不是很理想，除了協議很齊全，其他實在沒什麼長處。所以早就想買一台比較便宜的來整合看看。最近剛好換了 TP-Link Deco X20，就順便下單 C210 買來玩玩看，因為我覺得只要有 ONVIF 的規格，應該就可以整合進來，事實證明。沒有這麼簡單...

## 其實需要的是 HTTP、HTTPS

不知道是不是因為一隻一千塊而已，所以都不會有 HTTP 的協議，只有 RTSP。這個時候才注意到 Fibaro 只支援 HTTP、HTTPS ，完全忘記了...。不過沒關係，還是要試著整合看看，剛好工作上跟 Streaming 也沾上一點邊，所以還可以嘗試看看。

![](/assets/smart_home/cheap_ipcam_integration/1-1.png)

## Nginx Proxy Server

一開始是想透過 Nginx Proxy Server 從 RTSP 轉到 HTTP ，但是找來找去，只有 RTMP 的 module，秉著實驗精神，還是試試看。

寫了個 Dockerfile

```Dockerfile
FROM ubuntu:18.04

RUN apt -y upgrade && apt update && apt clean

RUN DEBIAN_FRONTEND=noninteractive apt -y install build-essential git gcc libssl-dev libpcre3 libpcre3-dev \
                  zlib1g zlib1g-dev openssl libxml2 libxml2-dev libxslt1-dev \
                  libgeoip-dev nginx make php ffmpeg

RUN mkdir /nginx-src

WORKDIR /nginx-src

RUN git clone https://github.com/arut/nginx-rtmp-module.git
RUN git clone https://github.com/nginx/nginx.git

WORKDIR /nginx-src/nginx

RUN ./auto/configure --prefix=/usr/share/nginx --conf-path=/etc/nginx/nginx.conf --http-log-path=/var/log/nginx/access.log --error-log-path=/var/log/nginx/error.log --lock-path=/var/lock/nginx.lock --pid-path=/run/nginx.pid --http-client-body-temp-path=/var/lib/nginx/body --http-fastcgi-temp-path=/var/lib/nginx/fastcgi --http-proxy-temp-path=/var/lib/nginx/proxy --http-scgi-temp-path=/var/lib/nginx/scgi --http-uwsgi-temp-path=/var/lib/nginx/uwsgi --with-debug --with-pcre-jit --with-http_ssl_module --with-http_stub_status_module --with-http_realip_module --with-http_auth_request_module --with-http_addition_module --with-http_dav_module --with-http_geoip_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_v2_module --with-http_sub_module --with-http_xslt_module --with-stream --with-stream_ssl_module --with-mail --with-mail_ssl_module --with-threads --add-module=../nginx-rtmp-module

RUN make && make install

WORKDIR /nginx-src/nginx/objs

RUN service nginx stop
RUN cp nginx /usr/sbin
RUN service nginx start
```

`失敗`

啟動後系統提示，不吃 RTSP

REF: https://github.com/arut/nginx-rtmp-module

## 運用 OctPrint 的經驗

不知道什麼是 OctPrint 沒關係，可以去[官網](https://octoprint.org/)逛逛，簡單說就是一個 Web 版的控制 3D Printer 的系統。因為這個系統可以在網頁顯示 Streaming，我就突發奇想，那可不可以把 RTSP 打到 Web 之後，再到 Fibaro 設定這個網頁的位置！

> 因為我買的是 TP-Link 的 IP CAM ，所以下面就用 Tapo 當作範例。

### 設置 TP-Link C210

首先要先設置好 IP Cam 然後進入 `進階設定 > 攝影機帳戶` 設定一組 username, password

![](/assets/smart_home/cheap_ipcam_integration/1-2.jpeg)

### 撰寫一個簡單的 PHP 網頁

已經忘了是哪裡參考來的程式碼，在這邊感謝那位大神！另外自己再改一下以符合 Fibaro 配置，以下程式碼，最終就是會運行一個 Process，且還能免打帳密登入 C210。可以參考[官網](https://www.tp-link.com/tw/support/faq/2680/)。不過這次就懶得包 Dockerfile 了。

```php
index.php
<?php
$ip = "127.0.0.1:554";
$endpoint = "";
$auth = "";

if (isset($_GET['usr'])) {
  if (isset($_GET['pwd'])) {
    $auth = $_GET['usr'].":".$_GET['pwd'];
    $auth.="@";
  }
} else {
  if (!isset($_SERVER['PHP_AUTH_USER'])) {
    header('WWW-Authenticate: Basic realm="My Realm"');
    header('HTTP/1.0 401 Unauthorized');
    echo 'User hits Cancel button';
    exit;
  }
}

if (isset($_SERVER['PHP_AUTH_USER'])) {
  $auth = $_SERVER['PHP_AUTH_USER'].":".$_SERVER['PHP_AUTH_PW'];
  $auth.="@";
}

if (isset($_GET['ip'])) {
  $ip = $_GET['ip'];
}

if (isset($_GET['endpoint'])) {
  $endpoint = $_GET['endpoint'];
}

$url = "rtsp://".$auth.$ip."/" . $endpoint;
$run = "/usr/local/bin/ffmpeg -i '$url' -f image2 -vframes 1 -pix_fmt yuvj420p pipe:";

header('Content-Type: image/jpeg');
passthru($run);
?>
```

### 啟動一個有 ffmpeg 的 Container

沒錯！這次又動用到 NAS 了，不開白不開，反正是 24H 運行，不用再另外開一台樹莓派了。配了一台 Ubuntu 18.04，然後在裡面安裝。

![](/assets/smart_home/cheap_ipcam_integration/1-3.png)

更新是一定要的

```bash
apt upgrade
apt update
```

安裝 php 與 ffmpeg，最後確定一下 ffmpeg 的執行檔路徑，不一樣的話要替換 index.php 中的 ffmpeg 路徑

```bash
apt -y install php ffmpeg
which ffmpeg
```

找個好位置把 index.php 放進去然後執行指令，這邊要特別注意，因為要讓 NAS 裡的 Container 直接對外，所以要注意兩點。

1. 網路要設定 `host`
1. 運行時帶上的 port 不能與 NAS 本身開的 port 有衝突

當然啦，後面加上 & 讓程式在背景跑

```php
php -S 0.0.0.0:8000 index.php &
```

接著在瀏覽器輸入網址就可以看到畫面囉！

 `http://<NAS-IP>:8000/?ip=<IPCAM-ADDRESS>:554&endpoint=stream1&usr=alan-home-tapo&pwd=<YOUR_PASSWORD>`

![](/assets/smart_home/cheap_ipcam_integration/1-4.png)

以上就處理的差不多了，準備整合到 Fibaro。

### 整合 Fibaro IP Cam

在新增 Device 的時候，隨便選一個 IP Cam 的型號，反正都沒差

![](/assets/smart_home/cheap_ipcam_integration/1-5.png)

然後在 Device 的詳細資訊中選擇 `http` 並且填入剛剛在瀏覽器輸入的網址，基本上就可以在 Fibaro 的畫面看到囉！

![](/assets/smart_home/cheap_ipcam_integration/1-6.png)

## Tapo 番外篇

因為這種類型的 IP Cam 都會把拍到的畫面送到外網，圖片中沒連到 Wifi 顯示為離線。一般是不一定要連 Wifi 也看得到的，因為 IP Cam 直接幫你開後門了。我不想！！所以就在 FortiGate 直接把他 Ban 掉了，而且我還是可以從 Fibaro 的系統中看到畫面的！

![](/assets/smart_home/cheap_ipcam_integration/1-7.jpeg)

後面再另外寫一篇吧！
