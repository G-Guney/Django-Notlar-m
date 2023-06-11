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
## Dosya İşlemleri (*)
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

## Model oluşturma ve Migrate kavramı

### Migrate Kavramı

Veritabanına oluşturulmuş tabloları kaydetmek göndermek için kullanırız.
+ terminalde şu kodu yazalım

```cmd
python manage.py migrate
```
+ migrate fonksiyonunu kullandığımızda yeni bir tablo oluşturulur.
+ tablolar oluşturulduktan sonra tablolar içerisine girelim.
+ Bir admin oluşturalım

```cmd
python manage.py createsuperuser
```

+ Oluşturduğumuz admin kullanıcı ile /admin bölümüne giriş yapabiliriz

### Model oluşturma

Courses isimli yeni bir uygulama oluşturalım

```cmd
python manage.py startapp courses
```
+ settings.py' içersinde kaydediyoruz.

+ courses klasöründeki models.py' da modellerimiz oluşturacağız.

+ modeller class'lar ile oluşturulur

+ class'ın her bir attribute'si veritabanındaki tablonun bölümüdür, class'ın kenidisde tabloya denk gelir

+ models.py'da Couser isimli bir class oluşturuyoruz. Class'ımızıaihtiyaca göre attr. ekleyeceğiz.

```py
class Course(models.Model):
    name = models.CharField(max_length=200, unique=True, verbose_name='Kurs Adı', help_text='Kurs adını yazınız')
    # Class oluşturduk ve name attributesi verdik.
    # unique=Eşsiz bir isim olması içi
    # verbose_name= Admin alanında gözükecek isim
    # help_text= Açıklayıcı bilgi
```

+ İhtiyacımız olan diğer attributeleri ekleyelim

```py
class Course(models.Model):
    name = models.CharField(max_length=200, unique=True, verbose_name='Kurs Adı', help_text='Kurs adını yazınız')
    description = models.TextField(blank=True, null=True)
    image = models.ImageField(upload_to="courses/%Y/%m/%d/", default="default_course_image.jpg")
    date = models.DateTimeField(auto_now=True)
    avaiable = models.BooleanField(default=True)
    # blank=True : Kullaıcı bu alanı boş bırakabilir, null=True : Veritabanında bu alan boş kalabilir
    # upload_to="courses/%Y/%m/%d/" : isimli klasöre yüklenecek ve tarih koyulacak
    # auto_now=True : Her düzenlemede tarih güncellenir
```

+ Modelimizi oluşturduktan sonra admin sayfasında kendi ismi ile gözükmesini istiyorsak;
```py
    def __str__(self):
        return self.name
```

+ Model'imiz hazır, Modelimizi veritabanına kaydetmemiz gerekli;
+ Öncelikle veritabanına kaydetmeye hazır hale getireceğiz

```cmd
python manage.py makemigrations
```

+ Hazır olan migration dosyalarını veritabanına kaydetme işlemi

```cmd
python manage.py migrate
```

+ Tablomuz Hazır..

### Admin alanında Modelleri görüntüleme

Oluşturulan modeli admin alanında görmek için onu tanıtmamız gerekli

+ Courses app'inde admin.py'a girelim ve Course'i tanıtalım

```py
from django.contrib import admin
from .models import Course

# Register your models here.

admin.site.register(Course)
```

### MEDIA dosyalarının konumunu ayarlama

Media dosyalarımız belirli bir klasörde olmalı bu sebeple yolunu settings.py içersinde belirtelim
+ Static_url yazdığımız yerin hemen altına yazabiliriz.

```py
STATIC_URL = '/static/'
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static')]

# MEDIA FILES

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

+ media'ya eklediğimiz url'lere ulaşmak içinse;
+ urls.py kısmında ayarlamamız gereken yerler var
+ öncelikle settings ve static import ediyoruz
+ sonrasında + static ile yapılandırıyoruz

_+static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)_: Medya dosyalarının doğru şekilde sunulması için gerekli olan URL yapılandırması. MEDIA_URL medya dosyalarının URL'sini belirtirken, MEDIA_ROOT medya dosyalarının yerel dosya sistemindeki konumunu belirtir. Bu yapılandırma, geliştirme ortamında medya dosyalarını sunmanızı sağlar

```py

from django.contrib import admin
from django.urls import path,include
from django.conf.urls.static import static # yeni
from django.conf import settings # yeni

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('pages.urls'))
] + static(settings.MEDIA_URL, document_root = settings.MEDIA_ROOT) # yeni

```

## Admin Alanını Özelleştirme

### Filtreleme, Arama vs işlemleri admin paneline ekleme

admin.py içeriğini güncelleyelim
```py
from django.contrib import admin
from .models import Course

# Register your models here.
@admin.register(Course)
class CourseAdmin(admin.ModelAdmin):
    search_fields = ('name','description') # 1
    list_display = ('name', 'avaiable')  # 2
    list_filter = ('avaiable',)  # 3
    
```
![admin](Django-Notlar-Resim/admin1.png)

### İnteface kurma

+ paketi yükle
```cmd
pip3 install django-admin-interface
```
+ installed_app kısmına ekle
```py
    'admin_interface',
    'colorfield',
```


## Kursları Siteye Ekleme(işlemleri tersten yapabilrisiniz)

### Courses sayfasını yapılandırma

+ Kurslar sayfası için yol belirleyelim, main urls.py'a ;

```py
path('courses/', include(courses.urls)),
```

+ Courses klasöründe urls.py oluşturalım, importları yapalım
+ Courses path'ını yazalım;

```py
path('', views.courses_list, name='courses'),
```
+ _navbar.html düzenlemesi;

```html
<li class="nav-item {% if '/' == request.path %} active {% endif %}"><a class="nav-link" href="{% url 'index' %}">Home</a></li>
<li class="nav-item {% if 'about' in request.path %} active {% endif %} "><a class="nav-link" href="{% url 'about' %}">About Us</a></li>
<li class="nav-item {% if 'courses' in request.path %} active {% endif %} "><a class="nav-link" href="{% url 'courses' %}">Courses</a></li> <!-- Düzenleme -->
<li class="nav-item"><a class="nav-link" href="teachers.html">Teachers</a></li>
<li class="nav-item"><a class="nav-link" href="contact.html">Contact</a></li>
<!-- Düzenleme -->
```

+ views.py ' da yolumuza fonksiyon oluşturalım.

```py
def courses_list(request):
    return render(request, 'courses.html')
```

+ Templates klasörümüzde courses.html oluşturalım
+ SITE içersinde blog-single.html içeriğini courses.html'e kopyalayalım.
+ include, block işlemlerini yaptık

### Courses sayfası içine dinamik olarak kursları ekleme

Bu işlem viesw.py içersinde olacak, view.py'a Course modelini import ederiz, bir değişkene Course modelindeki tüm nessneleri aktarırız. Sonra bunu bir dictinary aracılığıyla render edeceğimiz sayfaya göndeririz.

```py
from django.shortcuts import render
from . models import Course # 1-import et

# Create your views here.

def courses_list(request):
    courses = Course.objects.all() # 2-kursları değişkene ata

    context = {
        'courses': courses,
    } # 3-dict'e çevir

    return render(request, 'courses.html', context) # 4-dict'i render'a ekle

```

Şimdi courses.html içersinde for döngüsü ile kurslarımızı dinamik olarak gösterebiliriz
```html
                {% for course in courses %}

                <div class="col-lg-6 col-md-6 col-12">
                    <div class="course-item">
						<div class="image-blog">
							<img src="{{course.image.url}}" alt="" class="img-fluid">
						</div>
						<div class="course-br">
							<div class="course-title">
								<h2><a href="#" title="">{{course.name}}</a></h2>
							</div>
							<div class="course-desc">
								<p>{{course.description}}</p>
							</div>
						</div>
						<div class="course-meta-bot">
							<ul>
								<li><i class="fa fa-calendar" aria-hidden="true"></i> 6 Month</li>
								<li><i class="fa fa-users" aria-hidden="true"></i> 56 Student</li>
								<li><i class="fa fa-book" aria-hidden="true"></i> 7 Books</li>
							</ul>
						</div>
					</div>
                </div><!-- end col -->

                {% endfor %}
```

**Kursları eklerken en son gelenin ilk başta gözükmesi için;**

```py
courses = Course.objects.all().order_by('-date')
# .order_by(-date) eklendi
```

**Her bir kursun açıklamasaı farklı uzunluklarda olabilir.Görünüm bozulabilir bunu düzeltmek için**

```html
<p>{{course.description | truncatechars:100}}</p>
``` 

## İlişkiler

### OneToMany İlişkisi

#### Kursları kategorilere ayıracağız.
1. Kategori modeli oluşturacağız ve kurslarla kategori arasında bir ilişki olacak
_OneToMany_ ilişkisi: Bir kategori birden fazla kursta olabilir, fakat bir kurs tek kategoride olur şeklinde işme yapacağız.

```py
class Category(models.Model): # kategori modelini oluşturduk
    name = models.CharField(max_length=50, null=True)
    slug = models.SlugField(max_length=50, unique=True, null=True)
    
    def __str__(self):
        return self.name

class Course(models.Model):
    name = models.CharField(max_length=200, unique=True, verbose_name='Kurs Adı', help_text='Kurs adını yazınız')
    category = models.ForeignKey(Category, null=True, on_delete=models.DO_NOTHING) # burada ilişkilnedirdik
    description = models.TextField(blank=True, null=True)
    image = models.ImageField(upload_to="courses/%Y/%m/%d/", default="default_course_image.jpg")
    date = models.DateTimeField(auto_now=True)
    avaiable = models.BooleanField(default=True)

    def __str__(self):
        return self.name

# null = True kullanma sebebimiz yeni eklediğimiz bir alan oldupu için , boş olsada veri tabanında hata vermesim
```
2. Migrate işlemleri yapılır...
3. Admin panelinde görünmesi için admin.py'a kaydedilir

```py
@admin.register(Category)
class CategoryAdmin(admin.ModelAdmin):
    prepopulated_fields = {'slug' : ('name',)}
    # slug alanı name' den alınacak
    # name yanına virgül koyduk ki string olarak değil tuble olarak alsın
```

4. Admin panelinde kategoriler görüntülenir ve kurslara kategori eklenir

#### Şimdi kurslara tıkladığımızda kendi tekil sayfalarını yapacağız

1. Öncelikle yollarını belirtelim

```py
    path('<slug:category_slug>/<int:course_id>', views.courses_detail, name='course_detail'),
```

2. Course_detail view.py' yazalım
path yazarkan slug ve id parametrelerini kullandık bu sebeple request ile 2 parametre daha yazmamız gerekli
**category__slug yazarken cift underscore yazam sebebimiz ilk once kurs daha sonra kursın kategorisinin slug'ı 2 yol ilerliyor, id'de ise direkt kursun id'si**

```py
def course_detail(request, category_slug, course_id):
    course = Course.objects.get(category__slug = category_slug, id = course_id)
    context = {
        'course': course,
    }
    return render(request, 'course_detail.html', context)
```
3. course.html sayfasını yapalım
4. courses.html'de linke course.html 'i yazalım 2 şekilde olur

```html

<h2><a href="{% url 'course_detail' course.category.slug course.id %}" title="">{{course.name}}</a></h2> <!-- 1.yöntem -->

<h2><a href="/courses/<slug>/<id>" title="">{{course.name}}</a></h2> <!-- 2.yöntem -->

```
5. course.html verileri dinamik hale getirelim


### ManyToMany ilişkisi

Derslerimize etiketler ekleyeceğiz , bir ders birden fazla etiket alabilir veya bir etiket birden fazla derste olabilir. Bu sebeple ManyToMany ilişkisi kuracağiz

#### Tags modeli oluşturma

1. courses app'de models.py'a bir Tag adlı model oluşturalım

```py
class Tag(models.Model):
    name = models.CharField(max_length=50, null=True)
    slug = models.SlugField(max_length=50, unique=True, null=True)

    def __str__(self):
        return self.name
```

2. admin.py'a aktralım

```py

@admin.register(Tag)
class TagAdmin(admin.ModelAdmin):
    prepopulated_fields = {'slug' : ('name',)}

```

4. Migrate işlemlerini yapalım. Admin artık sayfasında görebiliriz
5. Admin syafasında bir kaç tane tag oluşturalım
6. course class'ımıza tags adlı yeni bir field ekleyelim

```py
class Course(models.Model):
    name = models.CharField(max_length=200, unique=True, verbose_name='Kurs Adı', help_text='Kurs adını yazınız')
    category = models.ForeignKey(Category, null=True, on_delete=models.DO_NOTHING)
    tags = models.ManyToManyField(Tag, blank=True, null=True) # Ekleme işlemi
    description = models.TextField(blank=True, null=True)
    image = models.ImageField(upload_to="courses/%Y/%m/%d/", default="default_course_image.jpg")
    date = models.DateTimeField(auto_now=True)
    avaiable = models.BooleanField(default=True)

    def __str__(self):
        return self.name
```

7. Admin alanından tag seçebiliriz.

## Kategori ve Tag'e göre siteyi dinamikleştirme

### Ktegori ile filtreleyerek Ekranan yazdırma

1. urls.py' a ekleme yapalım

```py
    path('categories/<slug:category_slug>', views.category_list, name='courses_by_category'),
```

2. templatelerimizde görebilmemiz için views'deki fonsksiyonların contextlerine categorileri tanıtmamız lazım

```py
from django.shortcuts import render
from . models import Course, Category

# Create your views here.

def courses_list(request):
    courses = Course.objects.all().order_by('-date')
    categories = Category.objects.all()

    context = {
        'courses': courses,
        'categories': categories,
    }

    return render(request, 'courses.html', context)

def course_detail(request, category_slug, course_id):
    course = Course.objects.get(category__slug = category_slug, id = course_id)
    context = {
        'course': course,
    }
    return render(request, 'course.html', context)

def category_list(request, category_slug):
    courses = Course.objects.all().filter(category__slug = category_slug)
    categories = Category.objects.all()
    context = {
        'courses': courses,
        'categories': categories,
    }
    return render(request, 'courses.html', context)

```

3. Course.html'de for döngüsü ile kategorilerinmizi ekrana çıktı alalım

```py
<div class="widget-categories">
	<h3 class="widget-title">Categories</h3>
	<ul>
		{% for category in categories %}
		<li><a href="#">{{category.name}}</a></li>
		{% endfor %}
	</ul>
</div>
```
4. href düzenleyelim

```py
<li><a href="{% url 'courses_by_category' category.slug %}">{{category.name}}</a></li>
```

### Tag ile filtreleyerek Ekranan yazdırma

**Kategori ile aynıdır fakat burada daha kısa bir yöntem kullanacağız**

1. yol oluşturalım

```py
path('tags/<slug:tag_slug>', views.tag_list, name='courses_by_tag')
```
2. Yolun fonksiyonunu oluşturalım

```py
def tag_list(request, tag_slug):
    courses = Course.objects.all().filter(tags__slug = tag_slug)
    tags = Tag.objects.all()
    context = {
        'courses': courses,
        'tags' : tags
    }
    return render(request, 'courses.html', context)
```
3. Querry'leri ekleyelim, context'i güncelleyelim

```py
from django.shortcuts import render
from . models import Course, Category, Tag 

# Create your views here.

def courses_list(request):
    courses = Course.objects.all().order_by('-date')
    categories = Category.objects.all()
    tags = Tag.objects.all()

    context = {
        'courses': courses,
        'categories': categories,
        'tags' : tags
    }

    return render(request, 'courses.html', context)

def course_detail(request, category_slug, course_id):
    course = Course.objects.get(category__slug = category_slug, id = course_id)
    context = {
        'course': course,
    }
    return render(request, 'course.html', context)

def category_list(request, category_slug):
    courses = Course.objects.all().filter(category__slug = category_slug)
    categories = Category.objects.all()
    tags = Tag.objects.all()
    context = {
        'courses': courses,
        'categories': categories,
        'tags' : tags
    }
    return render(request, 'courses.html', context)

def tag_list(request, tag_slug):
    courses = Course.objects.all().filter(tags__slug = tag_slug)
    tags = Tag.objects.all()
    context = {
        'courses': courses,
        'tags' : tags
    }
    return render(request, 'courses.html', context)

```

4. Dinamikleştirelim

```py 
<ul class="tags">
	{% for tag in tags %}
	<li><a href="{% url 'courses_by_tag' tag%}"><b>{{tag.name}}</b></a></li>
	{% endfor %}
</ul>
```

5. Seçili tag'ı kalın yazı ile yazmak

```py
{% for tag in tags %}
	<li><a href="{% url 'courses_by_tag' tag.slug %}">{% if tag.slug in request.path %}<b>{{tagname}}</b>{% else %}{{tag.name}}{% endif %}<a></li>
{% endfor %}
```

##Önemli : Bir önceki tag ve kategori işlemlerinde kendimiz tekrar ettik, kendini tekrar eden kodlardan kurtulmamız gerekir.Bunun için Class Based Views inceliyeceğiz.

## Class Views ve Function Views arasındaki fark

Class kullanımı inheritance avantajı sağlar. Daha kısa kod yazabiliriz.

İşimizi hangisi ile halledebiliyorsak onunla yaparız

**Class viewler django3'de dökümantasyondan faydanılır**

### TemplateView

#### as_view()

_Genelde static sayfalarda ve biraz veri göstermek isiyorsak kullanılır_

1. pages app'de views kısmına gelelim önceki fonksiyonları yorum satırına aldık

2. templateview import edelim

```py
from django.views.generic import TemplateView
```

3. AboutView isimli class oluşturalım TemplateViews'den miras alıcak
class'larda *return render* yerine *template_name* kullanılır

```py
class AboutView(TemplateView):
    template_name = 'about.html'
```

4. Şimdi yol uzantısını yazalım url.py' da import ettikten sonra *views.about* kısmını *AboutView.as_view()* ile değiştirelim

```py
from django.urls import path
from . import views
from pages.views import AboutView

urlpatterns = [
    path('', views.index, name='index'),
    path('about/', AboutView.as_view(), name='about'),
]

```

#### get_data_content()

Eğer dinamik olarak veri almak istiyorsak kullanırız

1. İndex viewsi için aboutta yaptığımız işlemleri yapalım
2. şimdi templateView'in get_context_data() metodunu kullanarak veri çekeceğiz. Çekeceğimiz verileri import etmemeiz gerekli.
Kursların available olanlarından son oluşturulanı başa olacak şekilde çekip sıraladık ve son  tanenisi slice ettik. Kursların available olanlarının sayısını çektik

```py
from typing import Any, Dict
from django.shortcuts import render
from django.views.generic import TemplateView
from courses.models import Course
# Create your views here.

# def index(request):
#     return render(request, 'index.html')

class IndexView(TemplateView):
    template_name = 'index.html'

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['courses'] = Course.objects.filter(avaiable = True).order_by('-date')[:2]
        context['total_course'] = Course.objects.filter(avaiable = True).count()
        return context
```

3. İndex sayfamızda çektiğimiz verileri yerleştirelim

```html
{% for course in courses %}
<div class="row align-items-center">
    <div class="col-xl-6 col-lg-6 col-md-12 col-sm-12">
        <div class="message-box">
            <h2>{{course.name}}</h2>
            <p>{{course.description}}</p>
            <a href="#" class="hover-btn-new orange"><span>Learn More</span></a>
        </div><!-- end messagebox -->
    </div><!-- end col -->
			
				
	<div class="col-xl-6 col-lg-6 col-md-12 col-sm-12">
        <div class="post-media wow fadeIn">
                <img src="{{course.image.url}}" alt="" class="img-fluid img-rounded">
        </div><!-- end media -->
    </div><!-- end col -->
</div>
{% endfor %}
```

4. Kursları sağlı sollu sıralamak için
forloop'un ilk elemanı ise şeklinde

```html
			{% for course in courses %}
            <div class="row align-items-center">

				{% if not forloop.first  %}

				<div class="col-xl-6 col-lg-6 col-md-12 col-sm-12">
                    <div class="post-media wow fadeIn">
                        <img src="{{course.image.url}}" alt="" class="img-fluid img-rounded">
                    </div><!-- end media -->
                </div><!-- end col -->
				
				{% endif %}

                <div class="col-xl-6 col-lg-6 col-md-12 col-sm-12">
                    <div class="message-box">
                        <h2>{{course.name}}</h2>
                        <p>{{course.description}}</p>
                        <a href="#" class="hover-btn-new orange"><span>Learn More</span></a>
                    </div><!-- end messagebox -->
                </div><!-- end col -->
				{% if forloop.first  %}
				
				<div class="col-xl-6 col-lg-6 col-md-12 col-sm-12">
                    <div class="post-media wow fadeIn">
                        <img src="{{course.image.url}}" alt="" class="img-fluid img-rounded">
                    </div><!-- end media -->
                </div><!-- end col -->
				{% endif %}
			</div>
			{% endfor %}
```

5. Kurs sayımızı dinamikleştirelim

totat_course ile çektiğimiz veriyi yerleştiriyoruz

```html
<div class="col-md-4 col-sm-4 col-xs-12">
	<span data-scroll class="global-radius icon_wrap effect-1 alignleft"><i class="flaticon-online"></i></span>
	<p class="stat_count">{{total_course}}</p>
	<h3>Courses</h3>
</div><!-- end col -->
```

## Class view devam

### Teacher app oluşturuyoruz

**app oluşturma aşamaları önceki konuda mevcut**

1. teachers model dosyamızda model oluşturacağız

```py
class Teacher(models.Model):
    name = models.CharField(max_length=50)
    title = models.CharField(max_length=50)
    description = models.TextField(blank=True)
    image = models.ImageField(upload_to="teachers/%Y/%m/%d", default="teachers/default_teacher_image.jpg")
    facebook = models.URLField(max_length=100, blank=True)
    twitter = models.URLField(max_length=100, blank=True)
    linkedin = models.URLField(max_length=100, blank=True)
    youtube = models.URLField(max_length=100, blank=True)

    def __str__(self):
        return self.name
```

2. Kurslar ile Ögretmenler arası ilişkiyi kuralım,Course models.py 'da Course Class'ına Teacher'ı ekleyelim

*Bir kursu bir öğretmen verebilir*
*Bir öğretmen birden fazla kurs verebilir*

```py
    teacher = models.ForeignKey(Teacher, null=True, on_delete=models.CASCADE)
```
**CASCADE : bir öğretmeni silersek , öğretmenin verdiği kurslarda silinecek**

3. Teachers'ın url ayarnını yapalaım ve html'i templates'e ekleyelim(teacher.html)

```py
from django.urls import path

urlpatterns = [
    path('', TeacherListView.as_view(), name='teacher'),
]

```

4. Teacher app'de ListView kullanacağız...

### ListView kullanımı

- Birden daha fazla nesne göstermek için kullanırız

1. TeacherListView isimli bir view oluşturduk, ListView'i import edelim ve Miras alalım

```py
from django.shortcuts import render
from django.views.generic.list import ListView
from teachers.models import Teacher

# Create your views here.

class TeacherListView(ListView):
    model = Teacher # içerikte hangi modeli kullanacağız
    template_name = 'teachers.html' # sayfa adımız (render edilecek yer)

```

2. Admin alanında bir kaç teacher oluşturalım

3. teacher.html'de içeriklerimiz yerleştirelim

```py
{% for teacher in object_list %}
				
<div class="col-lg-3 col-md-6 col-12">
	<div class="our-team">
		<div class="team-img">
			<img src="{{teacher.image.url}}">
			<div class="social">
				<ul>
					<li><a href="{{teacher.facebook}}" class="fa fa-facebook"></a></li>
					<li><a href="{{teacher.twitter}}" class="fa fa-twitter"></a></li>
					<li><a href="{{teacher.linkedin}}" class="fa fa-linkedin"></a></li>
					<li><a href="{{teacher.youtbe}}" class="fa fa-skype"></a></li>
				</ul>
			</div>
		</div>
		<div class="team-content">
			<h3 class="title">{{teacher.name}}</h3>
			<span class="post">{{teacher.title}}</span>
		</div>
	</div>
</div>

{% endfor %}

```
4. ListView Özellileri

+ contex_object_name = 'teachers' ;
    for döngüsünde object_list yerine teachers kullanabiliriz

+ quertset = Teacher.objects.all()[:1] ;
    sayfamızda 1 tane öğretmen gözükür

+ paginate_by = 1;
    paginate document'ini incele. next , previous yapar

**document'leri incele daha fazlası için çok yararlı bilgiler var**

### DetailView Kullanımı

Öğretmenlerin kendi özel sayfalarını oluşturacağız.

1. teacher.html isimli templates dosyamızı oluşturuyoruz

2. kullanacağımız path'ı yazalım

```py
from django.urls import path

from teachers.views import TeacherListView, TeacherDetailView # import ettik, şuan böyle bir class yok fakat birazdan view.py kısmında oluşturacağız

urlpatterns = [
    path('', TeacherListView.as_view(), name='teachers'),
    path('teacher/<int:pk>', TeacherDetailView.as_vieww(), name='teacher_detail') # buraya yazdık
]
```

3. Teachers.html'de teacher.html'e gidecek linki oluşturuyoruz

```html
<h3 class="title"><a href="{% url 'teacher_detail' teacher.pk %}"></a>{{teacher.name}}</h3>
```

**Django pk veya slug istiyor bu sebeple pk kullandık**

#### object yerine ilgili verinin ismini kullanmak

```py
class TeacherDetailView(DetailView):
    model = Teacher
    template_name = 'teacher.html'
    context_object_name = 'teacher' # burada belirttik
```
html'de değiştirelim

```html
	
	<div class="all-title-box">
		<div class="container text-center">
			<h1>{{teacher.name}}</h1><!-- <h1>{{object.name}}</h1> -->
		</div>
	</div>
```

4. Teacher verilerini html sayfamıza çekelim, imagei description vs..

5. Şimdi hoca tarafından verilen kursları sayfaya ekleyeceğiz.

#### Bir data kullanırken başka bir datayı'da ekleme işlemi(Farklı modellerle aynı sayfa içinde çalışma)

+ TeacherDetailView' e geleleim get_context_data() metodunu kullancağız
```py
from typing import Any, Dict
from django.shortcuts import render
from django.views.generic.list import ListView
from django.views.generic.detail import DetailView
from teachers.models import Teacher
from courses.models import Course  # kullanacağımız modeli import ettik

# Create your views here.

class TeacherListView(ListView):
    model = Teacher # içerikte hangi modeli kullanacağız
    template_name = 'teachers.html' # sayfa adımız (render edilecek yer)
    # paginate_by = 1
    # queryset = Teacher.objects.all()[:1]

    # def get_queryset(self):
    #     return Teacher.objects.all()[:2]

class TeacherDetailView(DetailView):
    model = Teacher
    template_name = 'teacher.html'
    context_object_name = 'teacher'

    def get_context_data(self, **kwargs): # g_c_d kullanarak bir dict oluşturduk ve courses isimli key'e aktardık
        context = super().get_context_data(**kwargs)
        context['courses'] = Course.objects.all()
        return context

```

+ html'de contexti kullananlım
```html
{% extends "partials/_base.html" %}
{% load static %}
	
	{% block content %}
	
	
	<div class="all-title-box">
		<div class="container text-center">
			<h1>{{teacher.name}}</h1>
		</div>
	</div>
	
    <div id="overviews" class="section wb">
        <div class="container">
            <div class="row"> 
                <div class="col-lg-12 blog-post-single">
					
					<div class="blog-author">
						<div class="author-bio">
							<h3 class="author_name">{{object.name}}</h3>
							<h5>{{teacher.title}}</a></h5>
							<p class="author_det">
								{{teacher.description}}
							</p>
						</div>
						<div class="author-desc">
							<img src="{{teacher.image.url}}" alt="about author">
							<ul class="author-social">
								<li><a href="{{teacher.facebook}}"><i class="fa fa-facebook"></i></a></li>
								<li><a href="{{teacher.twitter}}"><i class="fa fa-twitter"></i></a></li>
								<li><a href="{{teacher.linkedin}}"><i class="fa fa-linkedin"></i></a></li>
							</ul>
						</div>
					</div>
                </div><!-- end col -->
            </div><!-- end row -->

			<div class="row"> 

				{% for course in courses %}
				
                <div class="col-lg-4 col-md-6 col-12">
                    <div class="course-item">
						<div class="image-blog">
							<img src="{{course.image.url}}" alt="" class="img-fluid">
						</div>
						<div class="course-br">
							<div class="course-title">
								<h2><a href="#" title="">{{course.name}}</a></h2>
							</div>
							<div class="course-desc">
								<p>{{course.description}}</p>
							</div>
							<div class="course-rating">
								4.5
								<i class="fa fa-star"></i>	
								<i class="fa fa-star"></i>	
								<i class="fa fa-star"></i>	
								<i class="fa fa-star"></i>	
								<i class="fa fa-star-half"></i>								
							</div>
						</div>
						<div class="course-meta-bot">
							<ul>
								<li><i class="fa fa-calendar" aria-hidden="true"></i> 6 Month</li>
								<li><i class="fa fa-users" aria-hidden="true"></i> 56 Student</li>
								<li><i class="fa fa-book" aria-hidden="true"></i> 7 Books</li>
							</ul>
						</div>
					</div>
                </div><!-- end col -->

				{% endfor %}
            </div><!-- end row -->		
        </div><!-- end container -->
    </div><!-- end section -->
{% endblock content %}
```
**Burada tüm kursları çektik. Sonraki aşamalarda sadece öğretmene ait olanı çekeceğiz**

6. Admin panelinden kurslara teacher atayalım
7. Sadece Öğretmene ait olan kurslar için

```py
class TeacherDetailView(DetailView):
    model = Teacher
    template_name = 'teacher.html'
    context_object_name = 'teacher'

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        # context['courses'] = Course.objects.all()
        context['courses'] = Course.objects.filter(avaiable = True, teacher = self.kwargs['pk']) # filtreleme işlemi
        return context
```

8. Kursun detay sayfasına gittiğimizde kursu veren öğretmeni göstermek için

```html
<h3 class="author_name"><a href="#">{{course.teacher.name}}</a></h3>
```
_course'nın Teacher'ı mevcut ve context olarak göndermiştik_

9. Kurs sayfasında kursu veren öğretmenin sayfasnıa geçiş

*Kurs sayfasından gittiğimiz için başına coruse koymalıyız.*
```html
<h3 class="author_name"><a href="{% url 'teacher_detail' course.teacher.pk %}">{{course.teacher.name}}</a></h3>
```