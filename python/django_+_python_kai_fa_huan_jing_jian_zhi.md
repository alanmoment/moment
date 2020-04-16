# Django + Python 開發環境建置

# Python

開發Pythone時也可以像rvm，nvm的虛擬環境就是Virtualenv了，對於我這種有潔癖的人是超級需要的啊！

## Pip

---

download [get-pip.py](https://bootstrap.pypa.io/get-pip.py)
	
	$ python get-pip.py

## Virtualenv

---

**install**

	$ pip install virtualenv
	
> Linux or OS X need `sudo`

**setup**

	$ virtualenv ENV
	$ source ENV/bin/activate
	
啟動 virtualenv，從此只要在 virtualenv 下面安裝的 package 都只會存在于這個 virtualenv 當中。

## Virtualenvwrapper

1. 將所有的虛擬環境整合在一個目錄下。
2. 管理（新增、移除、複製）所有的虛擬環境。
3. 可以使用一個命令切換虛擬環境。
4. Tab 補全虛擬環境的名字。
5. 每個操作都提供允許使用者自訂的 hooks。
6. 可撰寫容易分享的 extension plugin 系統。

**install**

	$ pip install virtualenvwrapper

**新增虛擬環境**

	$ mkvirtualenv [-i package] [-r requirements_file] [virtualenv options] ENVNAME

**設定路徑**

	export PIP_VIRTUALENV_BASE=$WORKON_HOME

**列出所有的虛擬環境**

	$ lsvirtualenv
	
**移除虛擬環境**

	$ rmvirtualenv ENVNAME
	
**複製虛擬環境**

	$ cpvirtualenv ENVNAME TARGETENVNAME 

**啟動虛擬環境**

	$ workon [environment_name]

**離開虛擬環境**

	$ deactivate
	
**Python 3**

	$ which python3
	$ mkvirtualenv --python=/usr/bin/python3 python3
	
如果想要避免 pip 在沒有進入虛擬環境時被使用，可以在 ~/.bashrc 加上：

	export PIP_REQUIRE_VIRTUALENV=true

## Build Django

---

**install**

	$ pip install django
	
**requirements**

	$ pip install -r requirements.txt
	$ pip freeze > requirements.txt		
	
** start **

	$ django-admin.py startproject PROJECT_NAME

**run**

	$ python manage.py runserver

**create app**

	$ python manage.py startapp APP_NAME

**model**

settings.py

	INSTALLED_APPS = (
		...
		'customer'
		...
	)
	
models.py

	class Customer(models.Model):
    	content = models.TextField()

    	def __unicode__(self):
        	return self.content
    
`first`
     	
	$ python manage.py syncdb
	
`Your models have changes`

	$ python manage.py makemigrations

`generate`

	$ python manage.py migrate

`single change`

	$ python manage.py makemigrations MODEL

`rollback`

	$ python manage.py migrate system zero

## Django TestCase

**console usage**

	$ python manage.py test

## Reference

http://www.haogongju.net/art/2395132

> Feb 27th, 2015 12:16:10am