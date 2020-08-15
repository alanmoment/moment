# 神級照片備份方案

## 前言

從 2010 年開始就有拍照、錄影的習慣，這一路下來，累積了高達 2.4TB 以上的影音檔。而以前的備份方式超級簡易，就只是把照片匯入 Mac 系統的 `照片` 應用程式。因為我覺得這個程式設計得非常棒，讓我可以非常方便的管理影音檔。但就在踩了兩次坑之後，讓我下定決心要改變儲存方式！

過往的儲存方式是多年度一圖庫，到後來演變至一年度一圖庫（因為實在拍太多了一個圖庫超過 600 GB 鬼才有辦法維護）。但是這樣的大圖庫儲存方式，導致每次開啟圖庫瀏覽都非常的慢（當然也可能是因為我把圖庫放在 Apple Time Capsule）。所以中間改變作法，只用 `照片` 的匯出功能，因為這樣可以直接依照年、月、日來匯出影音檔，所以我近年的做法只是利用圖庫方便我執行匯入及匯出，主要瀏覽是透過 NAS。

但是隨著時間過去，MacOS 的更新已經導致我做過兩次圖庫的修復，這次更慘，在我更新完 Catalina 版本之後，已經無法開啟舊 MacOS 建立的圖庫了....這簡直就是悲劇。所以我決定重新審視我的儲存方案

## 前置作業

首先，需要有 MacOS、Synology NAS、AWS Glacier 這三種工具

以後的步驟則是：

拍照 > 依月份匯入 MacOS 的 `照片` > 依月份匯出至 NAS > 執行月份資料夾的壓縮程式 > 上傳到 AWS Glacier

沒錯！就是這樣，冷熱資料都顧到了，遺失也不怕了

## 壓縮照片程式

其實前面三個步驟，拍照就不說了，匯入與匯出，縮小至一個月，就算有相容問題，檔案也不大，好解決。但是壓縮程式這邊就需要解釋一下了。因為 AWS Glacier 是按照幾個計費方式

  1. 上傳請求數費用
  2. 下載的容量費用
  3. 固定儲存空間費用

### 上傳請求數費用

呼叫一次 API 上傳 archive 檔案就累計 1 ，如果是放在東京，則計價是 0.0571 / 1000次，所以我覺得要做事前壓縮，才會省錢。

### 下載的容量費用

當冷資料需要從 AWS Glacier 下載，那就會被收這筆費用，而且是以 GB 計價，這也是其中的因素讓我想以月來儲存壓縮檔，這樣我才可以比較精確的知道我要拿哪個月份的冷資料

### 固定儲存空間費用

相對便宜，每 GB 0.005 USD/月

`如果提前把存檔刪除，會被另外收一筆提前刪除的費用`

> 以上都可以到 AWS 官網查看費用 https://aws.amazon.com/tw/glacier/pricing/

### Docker

因為要跑程式，所以需要 container 來處理壓縮照片這件事，否則電腦都不用關機了，也因為有 NAS 所以相對好處理

選慣用的 ubuntu 來用

![](/assets/life/photo_backup_solution/ubuntu.png)

掛上資料夾

![](/assets/life/photo_backup_solution/volume.png)

container 啟動之後不支援中文，要在 `.bashrc` 修改，然後重新啟動

```export LC_ALL="C.UTF-8"```

再來就是會寫程式的好處了，寫了一隻 ShellScript 來處理照片資料夾的壓縮工作，我不做大檔，也因為一些隱私關係，有密碼總比沒有密碼來的強。

```
#! /bin/bash

# 要處理的資料夾檔名
name=2014

# 這邊 source 與 target 會是 /mnt 是因為我把外面的 photos 掛在 container 裡面了
# 要壓縮的目錄
source="/mnt/${name}"
# 壓縮檔案存放路徑
target="/mnt/glacier"

cd $source

for d in `ls ${source} | grep ^20` ; do
  echo $d

  # 不是目錄就跳開
  [ -d $d ] || break;

  # 壓縮檔存放的路徑如果不存在則建立
  [ -d ${target}/${name} ] || mkdir -p ${target}/${name}

  # 排除一些不必要的檔案，也把密碼直接帶入指令
  zip -9 -q -r --exclude="Thumbs.db" --exclude="*@eaDir*" --exclude="*.DS_Store*" -P <PASSWORD> ${target}/$name/${d}.zip ${d}

  echo "done"
done
```

但是解壓縮之後所有的日期都會變成解壓縮當天的日期..令人匪夷所思，但總比整個不見來的好。

最後就在 Synology 設定定期備份就好

![](/assets/life/photo_backup_solution/glacier_backup.png)