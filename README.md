<h1>一、Poetry 與其他套件安裝</h1>

1. 安裝：**poetry init**
2. 安裝 **django：poetry add django**
3. 進入 poetry 的虛擬環境：**poetry shell**
4. 安裝 djlint：**poetry add djlint** 設定vc  https://marketplace.visualstudio.com/items?itemName=monosans.djlint
5. 安裝 black：==poetry add black==  black 是排版工具，下載 https://marketplace.visualstudio.com/items?itemName=ms-python.black-formatter
6. 安裝 isort：**poetry add isort** 使用方式 https://blog.kyomind.tw/isort/
7. 安裝 ipython：**poetry add ipython**



VScode 設定存檔排版

    {
        "workbench.colorTheme": "Default Light Modern",
        "editor.fontSize": 17,
        "window.zoomLevel": 1,
        "[python]": {
            "editor.defaultFormatter": "ms-python.black-formatter",
            "editor.formatOnSave": true,
            "editor.codeActionsOnSave": {
                "source.organizeImports": "explicit"
            }
        },
        "emmet.includeLanguages": {
            "django-html": "html"
        },
        "[django-html]": {
            "editor.formatOnSave": true
        }
    }

---

<h1>二、建立 Django 專案叫做core，並讓他跑起來 </h1>

建立專案：**django-admin startproject core .**

啟動伺服器：**python manage.py runserver**

---

<h1>三、Django建立應用程式 App</h1>

1.建立專案

    python manage.py startapp posts


settings.py設定檔

    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',

        'posts.apps.PostsConfig'  #應用程式的設定檔類別
    ]


---

<h1>四、 產生 SQL </h1>

<h2>產生遷移（migrations）檔</h2>

    python manage.py makemigrations

<h2>套用遷移檔到 DB檔</h2>
    
    python manage.py migrate

<h2>將 DB 退到特定的版本</h2>

    python manage.py migrate first 0001


<h2>希望退到 first 0001 之前</h2>

    python manage.py migrate first zero

<h2>建立超級使用者</h2>

    python manage.py createsuperuser


------------

<h1>五、使用 Templates</h1>


1. 創建 **templates** 資料夾
2. settings 中 TEMPLATES 設定 
```python
TEMPLATES = [
    {
        .....
        "DIRS": [Path(BASE_DIR) / "templates", Path(BASE_DIR) / "info/templates"] 
        .....
    }
]
```
3. 建立 index.html 

```django
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Welcome</title>
</head>
<body>
    <h1>Welcome</h1>
    <p>{{ user_name }}</p>
    <p>{{ now }}</p>
    <p>{{ test }}</p>
</body>
</html>

```
4. 加入 views.py
```python
from datetime import datetime

from django.http import HttpResponse as https
from django.shortcuts import render


def index(request, user_name=None):
    message = user_name
    now = datetime.now()
    test = "t"
    return render(request, "index.html", locals())



```

---

# 六、url放入 base後的urls呼叫使用方式

如果我有一個 app 叫做 posts，裡面放入 def index，urls.py 需要以下方式:

使用放入方式是 ==from posts.views import index==



---

這是`urls.py`
```python 
#urls.py
from django.contrib import admin
from django.urls import path

from posts.views import index  # 修正导入路径

urlpatterns = [
    path("admin/", admin.site.urls),
    path("ww/",index),  
]

```



這是在posts資料夾裡面的`views.py`
```python 
# posts/views.py
from django.http import HttpResponse
from django.shortcuts import render


# Create your views here.
def index(request):
    user_name = "12354654648468468"
    return render(request, "first.html", locals())  
```



---------


# 七、html base 方式

透過 `{% block 變數 %}`  `{% endblock 變數 %}` 加入替換內容:


```html 
#first_base.html

<!DOCTYPE html>
<html lang="ja">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>
            {% block title %}
            {% endblock title %}
        </title>
    </head>
    <body>
        {% block body %}
        {% endblock body %}
    </body>
</html>

```

使用 `{% extends "繼承的html" %}` 加入繼承內容:


```html 
#first.html

{% extends "first_base.html" %}
{% block title %}mark個人網頁{% endblock %}
{% block body %}<p>現在時間: {{ user_name }}</p>{% endblock %}

```
