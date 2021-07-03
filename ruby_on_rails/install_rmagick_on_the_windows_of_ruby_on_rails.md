# Install rmagick on the Windows of Ruby on Rails

Windows 上不管是要安裝什麼 Ruby 的套件總是能出錯，這次就連很常見的 ImageMagick 還是會無法順利安裝，無言阿!!所以才會有人建議為了自己好，千萬不要在 Windows 上學習開發 Ruby on Rails，但是我是個窮鬼...買不起 Mac 只好硬是找方法。以下就是 Ruby on Rails 安裝 rmagick 的過程囉。

到 ImageMagick[官網](http://www.imagemagick.org/script/binary-releases.php#windows)下載

1. [ImageMagick-6.8.6-7-Q16-x86-static.exe](http://www.imagemagick.org/download/binaries/ImageMagick-6.8.6-7-Q16-x86-static.exe)
2. [ImageMagick-6.8.6-7-Q16-x86-dll.exe](http://www.imagemagick.org/download/binaries/ImageMagick-6.8.6-7-Q16-x86-dll.exe)

這兩個皆安裝在 C:\ImageMagick，用預設選項安裝即可

點選官網上的[download](http://www.imagemagick.org/script/download.php)>United States>[http://www.imagemagick.org/download](http://www.imagemagick.org/download)>[ImageMagick-6.8.6-7.tar.gz](http://www.imagemagick.org/download/ImageMagick-6.8.6-7.tar.gz)

下載、解壓縮後放置在 C:\ImageMagick 資料夾中，命名為 SourceCode

到[http://www.mingw.org/](http://www.mingw.org/)

點選 Navigation>[download](https://sourceforge.net/downloads/mingw)

下載 Looking for the latest version? [Download mingw-get-inst-20120426.exe (662.7 kB)](http://sourceforge.net/projects/mingw/files/latest/download?source=files)

將 MinGW 安裝在 C:\MinGW，並且勾選 MinGW Developer ToolKit，安裝完畢還要在系統的環境變數加上 C:\MinGW

![mingw](/assets/ruby_on_rails/install_rmagick_on_the_windows_of_ruby_on_rails/mingw.PNG)

最後用 gem 安裝 rmagick

```bash
gem install rmagick --platform=ruby -- --with-opt-lib=C:/ImageMagick --with-opt-include=c:/ImageMagick/SourceCode
```

![rmagick_down](/assets/ruby_on_rails/install_rmagick_on_the_windows_of_ruby_on_rails/rmagick_down.PNG)

Gemfile setup

```ruby
gem 'rmagick'
gem "carrierwave"
```

國外的完整[教學影片](http://www.youtube.com/watch?v=gEWAVlNCKhg&feature=youtu.be)

> Aug 12th, 2013 1:19:00am
