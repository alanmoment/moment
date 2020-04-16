# Install rmagick on the Windows of Ruby on Rails

Windows上不管是要安裝什麼Ruby的套件總是能出錯，這次就連很常見的ImageMagick還是會無法順利安裝，無言阿!!所以才會有人建議為了自己好，千萬不要在Windows上學習開發Ruby on Rails，但是我是個窮鬼...買不起Mac只好硬是找方法。以下就是Ruby on Rails安裝rmagick的過程囉。

到ImageMagick[官網](http://www.imagemagick.org/script/binary-releases.php#windows)下載

1. [ImageMagick-6.8.6-7-Q16-x86-static.exe](http://www.imagemagick.org/download/binaries/ImageMagick-6.8.6-7-Q16-x86-static.exe)
2. [ImageMagick-6.8.6-7-Q16-x86-dll.exe](http://www.imagemagick.org/download/binaries/ImageMagick-6.8.6-7-Q16-x86-dll.exe)

這兩個皆安裝在C:\ImageMagick，用預設選項安裝即可

點選官網上的[download](http://www.imagemagick.org/script/download.php)>United States>[http://www.imagemagick.org/download](http://www.imagemagick.org/download)>[ImageMagick-6.8.6-7.tar.gz](http://www.imagemagick.org/download/ImageMagick-6.8.6-7.tar.gz)

下載、解壓縮後放置在C:\ImageMagick資料夾中，命名為SourceCode

到[http://www.mingw.org/](http://www.mingw.org/)

點選Navigation>[download](https://sourceforge.net/downloads/mingw)

下載Looking for the latest version? [Download mingw-get-inst-20120426.exe (662.7 kB)](http://sourceforge.net/projects/mingw/files/latest/download?source=files)

將MinGW安裝在C:\MinGW，並且勾選MinGW Developer ToolKit，安裝完畢還要在系統的環境變數加上C:\MinGW

![mingw](https://lh5.googleusercontent.com/-KECm3GxukMI/UgfGIQ0TS6I/AAAAAAAAAF8/IWx8hpI8vI0/w598-h491-no/mingw.PNG)

最後用gem安裝rmagick

	$ gem install rmagick --platform=ruby -- --with-opt-lib=C:/ImageMagick --with-opt-include=c:/ImageMagick/SourceCode

![rmagick_down](https://lh5.googleusercontent.com/-0P8HhDZ4J5E/UgfGIcLG1wI/AAAAAAAAAGE/Jni3b_AAf9E/w921-h139-no/rmagick_down.PNG)

Gemfile setup

	gem 'rmagick'
	gem "carrierwave"

國外的完整[教學影片](http://www.youtube.com/watch?v=gEWAVlNCKhg&feature=youtu.be)

> Aug 12th, 2013 1:19:00am