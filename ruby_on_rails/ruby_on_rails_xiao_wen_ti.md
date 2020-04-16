# Ruby on Rails 小問題

安裝許久的 gem 太久沒更新了遇到一些麻煩，又剛好今天發現 Google Drive 以前可以用 Login 已經被取消了...天吶...

首先因為改了與 Google Drive 串接的 API 所以出現了 SSLError

	Faraday::SSLError (SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed):
	
這個找很久...後來是把 gem update 就解決了

	$ gem update
	$ gem update --system
	
而另外一個問題是一直很煩的，RVM 老是會出現一些 Warngin 今天惱了，找了方法來解決

	Warning! PATH is not properly set up, '/Users/alan/.rvm/gems/ruby-2.2.1/bin' is not at first place,
	         usually this is caused by shell initialization files - check them for 'PATH=...' entries,
	         it might also help to re-add RVM to your dotfiles: 'rvm get stable --auto-dotfiles',
	         to fix temporarily in this shell session run: 'rvm use ruby-2.2.1’.

最後還是把 RVM 整個移除重裝了，每次都忘記，這次就筆記起來， RVM 沒辦法用指令移除的話，可以直接整個砍掉，我這樣做是沒問題。

	$ rvm implode

or

	$ rm -rf ~/.rvm

重新安裝 RVM	

	$ curl -sSL https://get.rvm.io | bash -s stable
	$ rvm install 2.2.2
	$ rvm 2.2.2 --default
	$ gem install bundle
	
修改 .bash_profile
	
	## rvm
	PATH="$GEM_HOME/bin:$HOME/.rvm/bin:$PATH" # Add RVM to PATH for scripting
	[ -s ${HOME}/.rvm/scripts/rvm ] && source ${HOME}/.rvm/scripts/rvm
	
> Jun 4th, 2015 11:45:08pm 