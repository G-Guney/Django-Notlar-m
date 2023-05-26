# Django Notlarım

## Django ile SmartEdu Projesi Kurulum Aşaması

### 1. Sanal ortam kuruyoruz(Programlar güncellendiğinde vs. eski sürümlerle devam edebilmek için)

```cmd
python -m venv env
```

### 2. Sanal çerçeveniyi başlatmak
```cmd
env\Scripts\activate
```
### 3. Django kuruyoruz
```cmd
pip install Django
```
_var olan paketleri görmek için pip freeze kullanabilriz_

### 4. Pip uptade uyarası gelebilir bunun için pip'i update yapcağız
```cmd
python -m pip install --upgrade pip
```

### 5. Django projesi oluşturuyoruz
```cmd
django-admin startproject smartedu
```
_bu komuttan sonra smartedu isimli django projesi oluşturulur içersinde smartedu dosyası ve manage.py dosyası mevcut olur.Dıştaki smartedu isimli dosyamızın ismnini karışıklık olmaması için değiştirebiliriz. smartedu-con ismini koyuyoruz_

### 6. Projemizin dosyasının içine giriyoruz ve Django projesini başlatıyoruz
```cmd
cd smartedu_con

python manage.py runserver
```

**Mevcut Görünüm şu şekilde olmalı;**

![Kurulum1](Django-Notlar-Resim/kurulum1.png)

### 7. HTML-DESIGN sitesinde bir template almıştık bu dosyanın ismini SITE olarak değiştirip projemizin içerisine atıyoruz. Atacağımız yer SMARTEDU-PROJECT dosyası yanı en dış dosya.

**Mevcut Görünüm şu şekilde olmalı;**

![Kurulum1](Django-Notlar-Resim/kurulum2.png)

**NOT:** Projeyi kapattıktan sonra devam etmek için geri geldiğimizde env aktif etmemiz gerekli. 

## Uygulama oluşturma aşaması

Projemizde uyulamalarımız olacak bu uygulamar aracılığıyla daha kullanışlı kolay bir proje oluşturabiliriz

### 1. Uygulama oluşturma;
Pages isimli bir ugulama dosyası oluşturuyoruz

```cmd
python manage.py startapp pages
```
### 2. Uygulamayı kaydetme;
Unutmamamız gereken önemli bir aşama uyuglamayı oluştururduktan sonra bunu ana uygulamamızın içindeki settings.py dosyasının içinde mevcut olan INSTALLED_APPS dizisinin içersine kaydetmek.
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'pages.apps.PagesConfig', # <-- Buraya kaydetttik
]
```
### 3. İlk Sayfayı oluşturma;
Sayfa oluşturmak için bu sayfaların isteklerini ve karşılıklarını düzenleyen fonksiyonlar belirlememiz gerekli. Bu fonksiyonları views.py dosyası içersinde yazarız.
#### Şimdi index isimli bir fonksiyon oluşturacağız
```python
from django.shortcuts import render
from django.http import HttpResponse
# Create your views here.

def index(request):
    return HttpResponse("<h1> INDEX SAYFASI</h1>")
```

Bu fonksiyonun isteği ana uygulamanını urls.py dosyası içerisinde yapılır. Fakat daha uygun bir yöntem olarak. Her uygulamaya urls.py dosyası oluşturup buranın içersine path' lar yazabiliriz.
Sonrasında ise ana uygulamanın içersindeki urls.py dosyasına bağlayabiliriz.

#### Pages içersine url.py dosyası oluşturalım

İçersinde gerekli importlerı yapalım ve url yolumuzu view.py içersinde yazdığımız index fonksiyonu ile bağlayalım.

```python
from django.contrib import admin
from django.urls import path
from . import views # buradaki fonksiyonu kullanacağımız için bunuda importluyoruz
urlpatterns = [
    path('', views.index, name='index'),
    # path('domaine yazılan isim', (fonksiyon), 'isimlendirme)
]
```
#### Ana Uygylama içindeki urls.py' a pages urls.py bağlama işlemi

Include metodu kullanacağız import ediyoruz

```python
from django.contrib import admin
from django.urls import path,include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('pages/', include('pages.urls')), # <--
]
```
## Dosya İşlemleri 
### Templates kullanımı

Normalde httpresponse içeriğine bütün html kodları yazılabilir teorik olarak fakat bunun için ayrı bir kullanımımız mevcut.
Bu işlemi render ile yapacağız.
HttpResponse ile işimiz yok unu siliyoruz.
Yerine render kulllanacağız. Render işlemi ise bir request 'te karşılık yanıt verecek. Verdiği yanıt 'index.html olacak.
'index.html' i her uygulamanın templates klasörü vardır . Django bu klasörleri tanır buradakn çeker.

+ views.py değişikliğimiz;
```python
from django.shortcuts import render
# Create your views here.

def index(request):
    return render(request, 'index.html')
```

+ Pages uyuglaması içersinde templates klasörü oluşturduk,templates klasörü içerisine daha önce SITE isimli templates doyalarımız içersinden index.html'i kopyaladık
Dosya görünümü;

![templates](Django-Notlar-Resim/templates.png)

+ Runserver ile websitemizi görebiliriz

![website](Django-Notlar-Resim/website1.png)

**Bütün ugulamalara templates oluşturmak yerine ana bir templates dosyası oluşturup orada çalışmak daha kolay olucak. Bu sebeple ana bir templates oluşturacağız**

+ Pages uyg. içersindeki templates klasörünü smartedu_con içersine taşıyalım. Ana templates klasörü burada duracak.

![templates2](Django-Notlar-Resim/templates2.png)

+ Ana templates klasörümüzü settings.py içersinde tanımlamamız gerekli. settings.py içersinde TEMPLATES alanına tanımlıyoruz.
+ Tanımlama yaparken 'os' kullanacağımız için import etmeyi unutmayalım.

```python
import os
```
+ DIRS içersine yazacağiız kod : *os.path.join(BASE_DIR, 'templates')*

```py
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')], # <--Burası
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

### Static Kullanımı

Statik dosyalarımız da templates ile aynıdır. Templates yerine staatic isimli bir dosya oluştururuz ve css, js, img gibi dosyalarımızı burayada tutarız

1. static isimli dosya oluşturduk
2. static yolunu settings.py'da tanıtalım;
 ekleyeceğimiz kod : 
 _STATICFILES_URL = [os.path.join(BASE_DIR, 'static')]_
```py
# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/4.2/howto/static-files/

STATIC_URL = 'static/'
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static')] # <-Burası

```
3. SITE içersindeki statik dosyalarımızı klasörümüze taşıdik

![static](Django-Notlar-Resim/static1.png)

4. Templates dosyalarımızda static dosyalarımız yüklenmesi için kullanmamız gereken bir kod var:
```py
{% load static %} # <-- Kod.

<!DOCTYPE html>
<html lang="en">
....
```
5. Css, js, img gibi dosylar html'de yolları artık static içersidne olacağından yollarını düzenlememiz gerekli. Farklı yöntemler mevcut.
Birinci Yöntem:

```html
<link rel="stylesheet" href="{% static 'css/style.css' %}">
```

İkinci Yöntem
```html
<link rel="stylesheet" href="static/css/style.css'">
```

## HTML sayfalarında tekrarlamayı engelleme

### Block ve extend kullanımı
SITE klasörümüzden about.html'i templates klasörümüze aktaralım.

Tekrarlanan kısımlar için örneğin navbar, footer vs.

Sayfalar kendilerine özgü içerikleri block içersinde tutarlar

+ templates klasörü içersinde partials isimli klasör oluşturalım
+ İçerisine _base.html isimli bir dosya oluşturalım
+ _base.html içersine tüm sayfalarda ortak olan bölümleri alacağız
+ index.html içersinde <.!--end header --> bölümüne kadar olan kodları kesip _base.html dosyasına alıyoruz.
+ Her bir sayfanın kendine özgü içeriğinin nerede olacağını belirteceğiz
_Bu kod arasında kalan kısım sayfalara özgü içerik kısmıdır_

```django
{% block content %}
{% endblock %}
```

+ Şimdi index.html' den footer bölümünü ve footer aşağısındaki tüm geri kalan bölümü alıp block etikedinin alt tarafına yapıştırıyoruz.
+ Diğer sayfalarda kullanmak için _base.html extend edilmeli
+ Yeni bir sayfa açtığımızda aşağıdaki tag eklenmeli

```django
{% extends "partials/_base.html" %}
```
+ index.html içersine en üst kısma extend işlemini yapalım
+ içeriği block içersine alalım
+ static dosyalar kullandığı için load static tag'ini kullanalım

+ _base.html'den extend edilmiş index.html'imiz hazır.

### Navbar' ayırma işlemi(include kullanımı)

+ Partial klasörü içersinde _navbar.html oluşturalım
+ _base.html'den header bölümünü alıp _navbar.html içersine yapıştıralım
+ _navbar.html içersinde load static kullanalım
+ _base.html içersidne navbar kullanacağımız kısıma;

```django
{% include "partials/_navbar.html" %}
```
+ _navbar.html içersinde img yolllarını belirtmemiz gerekebilir
+ İşlem tamamlandı
**Dilersek footer içinde aynı işemleri yapabiliriz**

## Sayfalar arası gezinti

+ pages uyg. urls.py içersine yeni bir yol oluşturalım;

```python
path('about/', views.about, name='about'),
```
+ pages uyg. views.py içersinde about fonk. yazalım.

```py
def about(request):
    return render(request, 'about.html')
```
+ about.html' içersinde end-header'e kadar silelim
+ yerine ;
```py
{% extends "partials/_base.html" %}
{% load static %}
	
	{% block content %}
	......about içeriği
    {% endblock content %}
```

+ _navbar.html' e girelim
+ etiketlerin href'lerini ayarlayacağız

```html
<li class="nav-item active"><a class="nav-link" href="{% url 'index' %}">Home</a></li>
<li class="nav-item"><a class="nav-link" href="{% url 'about' %}">About Us</a></li>
```
+ active class'ını hangi sayfada isek ayarlamak için ise if metedu kullaabiliriz
+ index için == kullandık, about içinse in kullanmalıyız

```html
<li class="nav-item {% if '/' == request.path %} active {% endif %}"><a class="nav-link" href="{% url 'index' %}">Home</a></li>
<li class="nav-item {% if 'about' in request.path %} active {% endif %} "><a class="nav-link" href="{% url 'about' %}">About Us</a></li>
```

## Migrate Kavramı