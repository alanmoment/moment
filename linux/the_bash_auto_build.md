# The bash auto build

發覺自己在開發上常用的小工具都沒有在整理的，每次要用就都要回想，然後再一個一個找回來覺得很煩。尤其是現在工作上要管理十幾二十台後端。常常在那邊用得不順暢，才驚覺這樣真的不行！！

所以就花了點時間整理，順便用`Shell Script`寫成 auto build 練習一下。

### Git

在`setup.sh`的`setup_gitconfig`這個 method 主要是建立常用的`gitconfig`檔，也區分了家中與工作兩個環境去做建立

### Vim

最近想練習`vim`所以在網路上找了感覺不錯的`vim`也把他納進來。

> http://www.intelesyscorp.com/blog/awesome-vim-configuration

### gvimrc

`vim`延伸的設定檔

### bash_profile

這份是常用的自定義設定檔，除了在`console`前面顯示`git branch`及更新時間，因為時常搞錯當前環境，所以把`hostname`也加進來了，雖然長了一點，但是辨識度很不錯！！

### Install

預設是放在 `/$HOME/bash`，設定上非常簡單

```bash
sh setup.sh
```

打完收工！！

`Github`
https://github.com/alanmoment/bash

> Nov 18th, 2014 12:17:00am
