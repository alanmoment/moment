# 慶祝Octopress開張

搞了老半天終於搞定，來試試看好不好用!!! 立馬來一篇教學文。Github + Octopress

### 安裝 RVM

[Installing RVM](https://rvm.io/rvm/install)這邊有非常完整的教學。

### 建立Octopress

找一個讓程式碼安身的地方，我習慣放在home。(好吧這是來亂的)

	$ mkdir /home/ruby

接著就是到[Github](https://github.com/ "Github") 登入或註冊帳號，並且開一個新的Repositories，以我為範例就是 alanmoment.github.com，然後就是Ctrl+C 、Ctrl+V的快樂時光。

	$ rvm install 1.9.2 && rvm use 1.9.2
	$ git clone git://github.com/imathis/octopress.git octopress
	Initialized empty Git repository in /home/ruby/octopress/.git/
	remote: Counting objects: 10296, done.
	remote: Compressing objects: 100% (4511/4511), done.
	remote: Total 10296 (delta 5628), reused 9415 (delta 4937)
	Receiving objects: 100% (10296/10296), 2.28 MiB | 1.01 MiB/s, done.
	Resolving deltas: 100% (5628/5628), done.
	$ cd octopress
	Do you wish to trust this .rvmrc file? (/home/ruby/octopress2/.rvmrc)
	y[es], n[o], v[iew], c[ancel]>y
	Using /usr/local/rvm/gems/ruby-1.9.3-p374
	$ gem install bundler
	$ bundle install
	$ rake install

以上環境就建置完成囉。

緊接著要產生出blog需要的程式碼，輸入你剛開的Repositories，建立完成後會將branch從master切換到source這邊如果Repositories不對的話，整個blog會變得很怪無法使用。

	$ rake setup_github_pages
	Enter the read/write url for your repository
	(For example, 'git@github.com:your_username/your_username.github.io)
	Repository url:git@github.com:alanmoment/alanmoment.github.com.git

產生blog所需要的檔案，每次有修改或新增文章都要做這件事囉。

	$ rake generate
	$ rake deploy

我暫時先改了以下幾個變數，並且開了Google Plus、Facebook的3rd party

	$ vim _config.yml
	title: 艾倫之血汗屎
	subtitle: 新鮮的肝全奉獻在這了
	author: Alan

	google_plus_one: true
	facebook_like: true

建立一篇新的文章

	$ rake new_post['New Post']

最後就是把新增的、改過的東西通通放到Github上囉

	$ git add .
	$ git commit -m 'create octopress'
	$ git push origin source

### 修改About me

在這邊可以修改介紹自己的敘述，想要多臭屁都可以。

	$ rake new_page["about"]
	$ vim source/_includes/custom/asides/about.html

### 修改自己的大頭照

[My Gravatars](https://en.gravatar.com)申請帳號或登入，然後上傳一張自己可愛的大頭照

![](https://lh6.googleusercontent.com/-6smg7nJ9ycU/UbqOObkhXuI/AAAAAAAAAv8/kcK6WrGH9lU/s205-no/head.jpg)

接著在_config.yml配置email

	$ vim _config.yml
	# RSS feeds can list your email address if you like
	email: "alan.moment77@gmail.com"

Octopress預設會去抓My Gravatars有設定的email。太聰明啦!!

### 建立屬於自己的Domain

建立Custom Domain之前得先在source底下建立CNAME

	$ echo 'alanmoment.ocomm.com.tw' >> source/CNAME

接下來才是設定DNS，因為我是用Subdomain所以多了個A設定

	alanmoment.ocomm.com.tw.     IN     CNAME     alanmoment.github.io.
	alanmoment.github.io.        IN     A         207.97.227.245

若是不想建立，其實Github已經有提供連結給你用囉，棒呆啦!!以我為範例就是http://alanmoment.github.io/

### 建立Google Analytics

登入[Google Analytics](https://www.google.com/analytics)，並且建立一個分析帳戶，取得帳戶編號。設定非常簡單，只要在config內找到google_analytics\_tracking\_id打上編號。

	$ vim _config.yml
	$ google_analytics_tracking_id: UA-********-1
	
### 建立Octopress的Disqus Comments

[Disqus](https://disqus.com/)申請帳號或登入吧!接著修改config開啟你的Disqus，佈署之後就會在每一篇文章底下看到Comments囉，超方便。

	$ vim _config.yml
	# Disqus Comments
	disqus_short_name: alanmoment
	disqus_show_comment_count: true

### 更改Octopress的版型

官網的[repo](https://github.com/imathis/octopress/wiki/3rd-Party-Octopress-Themes)有提供非常多的版型，在這邊我是選擇了justin-kelly-theme。

	$ cd octopress
	$ git clone https://github.com/wallace/justin-kelly-theme.git .themes/justin-kelly-theme
	$ rake install['justin-kelly-theme']
	$ rake generate

最後吶喊~~ 我架好啦!!!! 打完收工。

呼~打文章真的還蠻辛苦的，話雖如此，總算有個窩囉，繼續努力!!!

6/19/2013 9:40:14 AM 

每次佈署都要打上兩段，好麻煩!!
	
	$ rake generate
	$ rake deploy

發現一個快速的方法

	$ rake gen_deploy

6/20/2013 10:44:35 PM 

因為有更新rails所以發生了一些錯誤無法使用
	
	$ rake preview
	root:/home/ruby/octopress(source)# rake preview
	rake aborted!
	You have already activated rake 10.1.0, but your Gemfile requires rake 0.9.6. Using bundle exec may solve this.
	/usr/local/rvm/gems/ruby-1.9.3-p374/gems/bundler-1.3.5/lib/bundler/runtime.rb:33:in `block in setup'
	/usr/local/rvm/gems/ruby-1.9.3-p374/gems/bundler-1.3.5/lib/bundler/runtime.rb:19:in `setup'
	/usr/local/rvm/gems/ruby-1.9.3-p374/gems/bundler-1.3.5/lib/bundler.rb:120:in `setup'
	/usr/local/rvm/gems/ruby-1.9.3-p374/gems/bundler-1.3.5/lib/bundler/setup.rb:7:in `<top>'
	/home/ruby/octopress/Rakefile:2:in `<top>'
	(See full trace by running task with --trace)

因為我裝了rake 10.1.0而設定檔還在rake 0.9.6所以我要修改Gemfile把rake改為10.1.0

	$ vim Gemfile
	gem 'rake', '~> 0.9.2'
	
替換為

	gem 'rake', '~> 10.1.0'

就又可以用囉!!!

	$ rake preview
	root:/home/ruby/octopress(source)# rake preview
	Starting to watch source with Jekyll and Compass. Starting Rack on port 4000
	[2013-06-20 22:47:24] INFO  WEBrick 1.3.1
	[2013-06-20 22:47:24] INFO  ruby 1.9.3 (2013-01-15) [i686-linux]
	[2013-06-20 22:47:24] INFO  WEBrick::HTTPServer#start: pid=5879 port=4000
	Configuration from /home/ruby/octopress/_config.yml
	Auto-regenerating enabled: source -> public

參考：

[Otcopress官網](http://octopress.org/docs/setup/)

> Jun 12th, 2013 1:59:00pm

> octopress, git