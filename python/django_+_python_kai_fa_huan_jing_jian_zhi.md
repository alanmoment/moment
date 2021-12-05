# Django + Python 開發環境建置

開發 Pythone 時也可以像 rvm，nvm 的虛擬環境就是 Virtualenv 了，對於我這種有潔癖的人是超級需要的啊！

## Pip Install

download [get-pip.py](https://bootstrap.pypa.io/get-pip.py)

```bash
python get-pip.py
```

## Virtualenv Install

```bash
pip install virtualenv
```

> Linux or OS X need `sudo`

**setup**

```bash
virtualenv ENV
source ENV/bin/activate
```

啟動 virtualenv，從此只要在 virtualenv 下面安裝的 package 都只會存在于這個 virtualenv 當中。

## Virtualenvwrapper

1. 將所有的虛擬環境整合在一個目錄下。
2. 管理（新增、移除、複製）所有的虛擬環境。
3. 可以使用一個命令切換虛擬環境。
4. Tab 補全虛擬環境的名字。
5. 每個操作都提供允許使用者自訂的 hooks。
6. 可撰寫容易分享的 extension plugin 系統。

安裝

```bash
pip install virtualenvwrapper
```

新增虛擬環境

```bash
mkvirtualenv [-i package] [-r requirements_file] [virtualenv options] ENVNAME
```

設定路徑

```bash
export PIP_VIRTUALENV_BASE=$WORKON_HOME
```

列出所有的虛擬環境

```bash
lsvirtualenv
```

移除虛擬環境

```bash
rmvirtualenv ENVNAME
```

複製虛擬環境

```bash
cpvirtualenv ENVNAME TARGETENVNAME
```

啟動虛擬環境

```bash
workon [environment_name]
```

離開虛擬環境

```bash
deactivate
```

**Python 3**

```bash
which python3
mkvirtualenv --python=/usr/bin/python3 python3
```

如果想要避免 pip 在沒有進入虛擬環境時被使用，可以在 ~/.bashrc 加上：

```bash
export PIP_REQUIRE_VIRTUALENV=true
```

## Build Django

**install**

```bash
pip install django
```

**requirements**

```bash
pip install -r requirements.txt
pip freeze > requirements.txt
```

**start**

```bash
django-admin.py startproject PROJECT_NAME
```

**run**

```bash
python manage.py runserver
```

**create app**

```bash
python manage.py startapp APP_NAME
```

**model**

settings.py

```python
INSTALLED_APPS = (
  ...
  'customer'
  ...
)
```

models.py

```pythoon
class Customer(models.Model):
  content = models.TextField()

  def __unicode__(self):
    return self.content
```

同步資料庫

```bash
python manage.py syncdb
```

`Your models have changes`

```bash
python manage.py makemigrations
```

`generate`

```bash
python manage.py migrate
```

`single change`

```bash
python manage.py makemigrations MODEL
```

`rollback`

```bash
python manage.py migrate system zero
```

## Django TestCase

**console usage**

```bash
python manage.py test
```

## Reference

http://www.haogongju.net/art/2395132

> Feb 27th, 2015 12:16:10am
