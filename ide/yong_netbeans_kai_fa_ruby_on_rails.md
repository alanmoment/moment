# 用NetBeans開發Ruby On Rails

NetBeans除了能開發PHP、Java、..等等的這些程式語言，其實也早就有Plugin能支援開發Ruby哦。

### 下載RailsInstaller

[RailsInstaller][]有完整的開發環境。一包就搞定。

### 下載Ruby and Rails Plugin

從官網的[Plugins][]下載完畢後從NetBeans的功能選項中選擇

Tools→Plugins

![plugins](https://lh4.googleusercontent.com/-pW5jRlN8K2o/UcVfYNXsvjI/AAAAAAAAAB0/KXIKyjfIzcs/w965-h827-no/netbeans-ror-plugin.PNG)

在安裝其他的plugins之前，必須先選擇安裝org-jruby-jruby.jar，讓NetBeans可以支援Ruby

![org-jruby-jruby.jar](https://lh5.googleusercontent.com/-H4CXHOcRxvk/UcVfYEQYiTI/AAAAAAAAAB8/3DOZiUqpb-o/w942-h827-no/netbeans-ror-plugin-1.PNG)

再來安裝其他的plugins就不會出現錯誤訊息

![Ruby and Rails plugins](https://lh4.googleusercontent.com/-9t0LTCB8sAo/UcVfYquTLAI/AAAAAAAAACA/8IeYPpbY-gU/w971-h827-no/netbeans-ror-plugin-2.PNG)

全部安裝完畢後，就可以在功能選項中建立Ruby On Rails的Application囉。

File→New Project

![New Project](https://lh6.googleusercontent.com/-AbB-Sg-KbrE/UcVg8LIWKII/AAAAAAAAACc/7E2iTH382Ec/w909-h827-no/%25E6%2593%25B7%25E5%258F%2596.PNG)

雖然我使用NetBeans已經很長一段時間了，但是主要用他來開發PHP，開發PHP是很好用沒錯。要用他來開發RoR是有點勉強阿...

所以其實我裝完之後就沒再使用了，我個人比較推薦[Aptana Studio][]，畫面看起來也比較舒服。

## 發生錯誤 ##

> 6/23/2013 4:57:01 PM 

建立project之後執行run，發現console有錯誤訊息如下

![](/assets/netbeans_problem.png)

	WARN  Could not determine content-length of response body. Set content-length of the response or set Response#chunked = true

在C:\RailsInstaller\Ruby1.9.3\lib\ruby\1.9.1\webrick\httpresponse.rb搜尋

	if chunked? || @header['content-length']

替換為

	if chunked? || @header['content-length'] || @status == 304 || @status == 204

即可解決

參考：[http://richardjoo.wordpress.com/2013/01/15/warn-could-not-determine-content-length-of-response-body-set-content-length-of-the-response-or-set-responsechunked-true/](http://richardjoo.wordpress.com/2013/01/15/warn-could-not-determine-content-length-of-response-body-set-content-length-of-the-response-or-set-responsechunked-true/)

> 6/23/2013 11:57:01 PM 

當更改固定首頁的時候出現錯誤訊息如下

	ExecJS::RuntimeError in Home#index

可以到C:\RailsInstaller\Ruby1.9.3\lib\ruby\gems\1.9.1\gems\execjs-1.4.0\lib\execjs\runtimes.rb搜尋cscript //E:jscript //Nologo //U

	JScript = ExternalRuntime.new(
		:name        => "JScript",
		:command     => "cscript //E:jscript //Nologo //U",
		:runner_path => ExecJS.root + "/support/jscript_runner.js",
		:encoding    => 'UTF-16LE' # CScript with //U returns UTF-16LE
	)

替換成

    JScript = ExternalRuntime.new(
        :name        => "JScript",
        :command     => "cscript //E:jscript //Nologo",
        :runner_path => ExecJS.root + "/support/jscript_runner.js",
        :encoding    => 'UTF-8' # CScript with //U returns UTF-16LE
    )

參考：[http://stackoverflow.com/questions/14283465/issue-with-upgrade-to-rails-3-2-11-execjsruntimeerror-in-static-pageshome](http://stackoverflow.com/questions/14283465/issue-with-upgrade-to-rails-3-2-11-execjsruntimeerror-in-static-pageshome)

[RailsInstaller]: http://railsinstaller.org/
[Plugins]: http://plugins.netbeans.org/plugin/38549/ruby-and-rails
[Aptana Studio]: http://www.aptana.com/

> Oct 4th, 2013 12:00:00pm