# 在 macOS 修改影音檔案的建立時間

在痛苦了好多年，每次在把影片存在 Photos 的時候，建立時間就會錯亂，會造成整個排序非常畸形，有強迫症的我，完全無法接受。

原因在於 Sony 的手持攝影機的影音格式是 `M2TS`，近年的 macOS 支援度已經沒那麼高了 Audio 在匯入時會被消音。就在最新版 macOS Catalina 10.15.x 也已經完全無法匯入了，會出現錯誤訊息

![](/assets/life/update_date_on_mac/update_date_on_mac1.png)

就變成，得在本機先轉檔。好險有佛心軟體 `AVCHD to Mov Lite`，我認為轉檔的效果還不錯。重點是免費的！但是轉完檔案後的建立時間全部都變成轉檔的日期當天。我整個暈倒...。所以後來只好找方法去修改影音檔案的日期，找好久才找到。而我的檔名就是建立的時間，但是一個一個檔案的修改，我也會暈倒。只好運用工作技能，寫一隻 ShellScript 自動的修改時間。

```bash
#! /bin/bash

folder=$1

for file in `ls $folder | grep .mov` ; do
  [ -d $file ] || break;
  fulltime=$(echo $file | sed "s/\(.*\).mov/\1/g")
  year=${fulltime:0:4}
  month=${fulltime:4:2}
  day=${fulltime:6:2}
  hour=${fulltime:8:2}
  min=${fulltime:10:2}
  sec=${fulltime:12:2}
  echo $file:$fulltime-$year-$month-$day-$hour-$min-$sec
  SetFile -d "$month/$day/$year $hour:$min:$sec" $folder/$file
  touch -t ${year}${month}${day}${hour}${min}.${sec} $folder/$file
  # touch -a -m -t ${year}${month}${day}${hour}${min}.${sec} $file
done
```

現在只要轉完檔，再跑一下 ShellScript 就可以匯入 Photos 了。爽！！

也可以讓程式在背景跑

```bash
nohup photo-handler.sh > nohup.out 2>&1 &
```

## 運用 exiftool 修改日期時間

其實要修改日期時間，還有另外一個方法，但是要另外安裝 exiftool，下面就列出一些我最常使用的指令

檢查 Meta Data

```bash
exiftool -s <file-name>
```

如果無法順利的改變檔案的時間，可以先找出日期資料，再依照結果修改

```bash
exiftool -time:all -a -G0:1 -s <file-name>.mp4
[File:System]   FileModifyDate                  : 2020:08:15 12:07:40+08:00
[File:System]   FileAccessDate                  : 2020:08:15 12:07:40+08:00
[File:System]   FileInodeChangeDate             : 2020:08:15 12:07:40+08:00
[H264]          DateTimeOriginal                : 2020:08:08 14:08:56+08:00
[H264:GPS]      GPSTimeStamp                    : 16:06:34.392

exiftool "-CreateDate>FileModifyDate" "-CreateDate>FileInodeChangeDate" "-CreateDate>FileCreateDate" <file-name>.mp4
```

依指定的影音格式改變影片的建立、修改日期，`.` 可以是資料夾，`-ext` 也可以改成 mp4 或是其他格式

```bash
exiftool -ext m2ts "-fileCreateDate<dateTimeOriginal" "-fileModifyDate<dateTimeOriginal" .
```

依日期改變 m2ts 的檔名

```bash
exiftool -ext m2ts "-FileName<DateTimeOriginal" -d "%Y%m%d%H%M%S.m2ts" <file-name>.m2ts
```
