# Netbeans console中文亂碼解決方法

以我為範例我是安裝在
 
	C:\Program Files (x86)\NetBeans 7.3.1

打開

	C:\Program Files (x86)\NetBeans 7.3.1\etc\netbeans.conf


編輯其中的這一行：

netbeans\_default\_options="-J-client -J-Xss2m -J-Xms32m -J-XX:PermSize=32m -J-XX:MaxPermSize=200m -J-Xverify:none -J-Dapple.laf.useScreenMenuBar=true **-J-Dfile.encoding=utf8** --fontsize 12"

加入粗體的部分，然後重新啟動Netbeans。

> Aug 26th, 2013 10:45:00am