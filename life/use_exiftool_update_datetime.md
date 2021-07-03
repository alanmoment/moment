檢查 Meta Data

```
exiftool -s <file-name>
```

如果無法順利的改變檔案的時間，可以先找出日期資料，再依照結果修改

```
exiftool -time:all -a -G0:1 -s <file-name>.mp4
[File:System]   FileModifyDate                  : 2020:08:15 12:07:40+08:00
[File:System]   FileAccessDate                  : 2020:08:15 12:07:40+08:00
[File:System]   FileInodeChangeDate             : 2020:08:15 12:07:40+08:00
[H264]          DateTimeOriginal                : 2020:08:08 14:08:56+08:00
[H264:GPS]      GPSTimeStamp                    : 16:06:34.392

exiftool "-CreateDate>FileModifyDate" "-CreateDate>FileInodeChangeDate" "-CreateDate>FileCreateDate" <file-name>.mp4
```

依指定的屬性改變照片的建立、修改日期

```
exiftool "-fileCreateDate<modifyDate" "-fileModifyDate<modifyDate" .

exiftool "-fileCreateDate<dateTimeOriginal" "-fileModifyDate<dateTimeOriginal" .

exiftool -ext m2ts "-fileCreateDate<dateTimeOriginal" "-fileModifyDate<dateTimeOriginal" .
```

改變 m2ts 的檔名

```
exiftool -ext m2ts "-FileName<DateTimeOriginal" -d "%Y%m%d%H%M%S.m2ts" 20140112210853.m2ts
```

在背景跑 shellscript

```
nohup /mnt/shellscript/photo-handler.sh > nohup.out 2>&1 &
```