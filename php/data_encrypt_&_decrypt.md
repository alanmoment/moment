# Data Encrypt & Decrypt

今天把資料的加解密整理了一下也丟到 [Github][1] 了，不過只有 Server 的，雖然也可以玩玩看加解密，但也蠻奇怪的，加密端與解密端應該是要不同的平台，還要再找時間把 Android 的加密也整理一下。不曉得又要拖到什麼時候...哈。

只要在自己的 `composer.json` 加上設定檔就可以囉！

	{
	    "repositories": [
	        {
	            "type": "git",
	            "url": "https://github.com/alanmoment/rsa-util.git"
	        }
	    ],
	    "require": {
	        "alanmoment/rsa-util": "dev-master"
	    },
	    "minimum-stability": "dev",
	    "autoload": {
	        "classmap": [
	            "vendor/alanmoment"
	        ]
	    }    
	}

範例程式碼

	# index.php
	require __DIR__ . '/vendor/autoload.php';
	  
	use Rsa\RsaUtil;
	  
	$RsaUtil = new RsaUtil();
	$RsaUtil->setKeyStorePath("./keystores/");
	$encrypt = $RsaUtil->generate()->encrypt("I am test data.");
	echo $encrypt;
	  
	$decrypted = $RsaUtil->decrypt($encrypt);
	echo $decrypted;

[1]: https://github.com/alanmoment/rsa-util

> May 5th, 2015 11:59:25pm