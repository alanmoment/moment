# 在 macOS 上修改影音檔案的建立時間

在痛苦了好多年，每次在把影片存在 Photos 的時候，建立時間就會錯亂，會造成整個排序非常畸形，有強迫症的我，完全無法接受。

原因在於 Sony 的手持攝影機的影音格式是 `M2TS`，近年的 macOS 支援度已經沒那麼高了 Audio 在匯入時會被消音。就在最新版 macOS Catalina 10.15.x 也已經完全無法匯入了，會出現錯誤訊息

![](/assets/life/update_date_on_mac/update_date_on_mac1.png)

就變成，得在本機先轉檔。好險有佛心軟體 `AVCHD to Mov Lite`，我認為轉檔的效果還不錯。重點是免費的！但是轉完檔案後的建立時間全部都變成轉檔的日期當天。我整個暈倒...。所以後來只好找方法去修改影音檔案的日期，找好久才找到。而我的檔名就是建立的時間，但是一個一個檔案的修改，我也會暈倒。只好運用工作技能，寫一隻 ShellScript 自動的修改時間。

```
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