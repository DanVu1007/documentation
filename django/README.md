README ấn ctr+shift+v sẽ ra preview của README
# Bước 1 – Cài đặt Django

### Tạo thư mục django-apps (hoặc bất kỳ tên nào khác) để chứa ứng dụng Django
```
mkdir django-apps
```
```
cd django-apps
```
### Ở trong thư mục django-apps, tạo môi trường ảo có tên env
```
virtualenv env
```
### Sau đó activate môi trường này:
```
. env/bin/activate
```
### tắt activate môi trường:
```
deactivate
```
*Khi đã được activate thành công thì prompt sẽ có phần (env) ở phía trước:*
`(env) danva@aht.local@CAS-065`

### Bên trong môi trường này, ta sẽ cài đặt Django bằng pip:
```
pip install django
```
### Sau đó kiểm tra xem Django đã được cài đặt thành công chưa:
```
django-admin --version
```
`kết quả vậy là thành công`
`Output:`
`3.0.6`


# Bước 2 – Thay đổi cấu hình tường lửa

### Đối với tường lửa UFW, ta có thể chạy lệnh dưới đây để mở một port:
```
sudo ufw allow 8000
```

# Bước 3 – Khởi động project

### Tạo một ứng dụng
Bây giờ ta có thể khởi tạo một ứng dụng bằng django-admin – một công cụ command line cho các tác vụ quản trị trong Python. Sau đó dùng lệnh `startproject` để tạo một cấu trúc thư mục project cho website.
Chạy lệnh sau trong thư mục `django-apps`:
cd to `django-apps`
```
django-admin startproject testsite
```
Lưu ý: Khi chạy lệnh django-admin startproject <projectname>, cả thư mục project và package project đều sẽ có tên là <projectname>. Ngoài ra lệnh cũng tạo một project trong thư mục mà lệnh được chạy. Nếu có tham số <destination> trong lệnh thì Django sẽ sử dụng thư mục đích được chỉ định làm thư mục project, đồng thời tạo manage.py và package project bên trong đó.
Trong đó:
    `__init__.py` khởi tạo project Python
    `asgi.py` chứa cấu hình phần deploy của Asynchronous Server Gateway Interface (ASGI), cung cấp một tiêu chuẩn cho các ứng dụng đồng bộ hoặc không đồng bộ.
    `settings.py` mô tả cấu hình cài đặt của Django, cho phép Django biết thiết lập nào đang khả dụng.
    `urls.py` chứa một danh sách urlpatterns, có nhiệm vụ định tuyến và ánh xạ URL vào các views tương ứng.
    `wsgi.py` chứa cấu hình cho Web Server Gateway Interface (WSGI), cung cấp một tiêu chuẩn cho các ứng dụng Python đồng bộ.
    Người dùng có thể thay đổi file asgi.py hoặc wsgi.py để phù hợp với nhu cầu deploy của mình.

### Tạo một module
```
python3 manage.py startapp polls
```

# Bước 4 – Cấu hình Django

### Thêm địa chỉ IP vào danh sách ALLOWED_HOSTS
Vào thư mục sau ` ~/django-apps/testsite/testsite/settings.py`
Sửa nội dung:
'your-server-ip' thành 127.0.0.1
`Edit the line below with your server IP address
ALLOWED_HOSTS = ['your-server-ip']
...`

### Migrate data
```
python3 manage.py migrate
```

### Thêm địa chỉ IP vào danh sách ALLOWED_HOSTS
```
python3 manage.py createsuperuser
```
# Bước 5 – Truy cập ứng dụng web Django

Sau khi cấu hình xong, chuyển về thư mục chứa file manage.py:
```
cd ~/django-apps/testsite/
```
Sau đó chạy lệnh dưới đây, trong đó thay your-server-ip thành địa chỉ IP tương ứng của server:
```
python3 manage.py runserver your-server-ip:8000
```


# Các lưu ý:

#### Url view
Trong `urls.py của app` thêm nội dung:
```
urlpatterns = [
    path('admin/', admin.site.urls),
    path('megamenu/', include('megamenu.urls')),
    path('', include('home.urls')),  # Khai báo thêm về urls của module home
]
```
Trong `urls.py` của `module home` thêm nội dung:
```
from django.urls import path
from . import views

urlpatterns = [
   path('', views.index)
]

```
Trong `views.py` của `module home` thêm nội dung:
```
from django.shortcuts import render
# Create your views here.
def index(request):
    context = {
        'year': 2023,
    }
    return render(request, 'pages/home.html', context)
```

Trong đó lưu ý `template/pages/home.html` sẽ được kế thừa từ `template/pages/base.html`
```
home
    |__templates
                |__base.html
                |__home.html
    |__urls.py
    |__views.py
```

#### Static
Thêm nội dung sau vào file `settings.py`
Đầu tiên import os
```
import os
```
Sau đó khai báo STATICFILES_DIRS
```
STATICFILES_DIRS = [
   os.path.join(BASE_DIR, "static"),
]
```
Tải các file cần import nhự `css` hay `js` vào folder `static`

Sau đó thêm đoạn code sau trong thẻ `<head>`
```
{% load staticfiles %}
```
Sử dụng css
```
<link rel="stylesheet" href = " {% static 'css/bootstrap.min.css' %}" type="text/css">
```
Sử dụng js
```
<script src="{% static 'js/bootstrap.bundle.min.js' %}"></script>
```
Có thể kiểm tra được rồi

#### Media
Kiểm tra và thêm phần Cấu hình Media trong `settings.py`
```
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

Trong thư mục gốc `app/urls.py` sửa nội dung urls.py :
Thêm dòng sau để phục vụ các tệp media trong môi trường phát triển
```
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

#### Admin module
Muốn hiển thị nội dung quản lý trong admin django đăng kí nội dung sau trong module:  `megamenu/admin.py`
```
from django.contrib import admin

# Register your models here.
from .models import MegaMenu, MenuItem

class MegaMenuAdmin(admin.ModelAdmin):
    list_display = ('name', 'use_on_fe', 'banner')  # Các field cần hiển thị

class MenuItemAdmin(admin.ModelAdmin):
    list_display = ('name', 'link', 'categories', 'content', 'show_categories', 'level_category', 'sequence') # Các field cần hiển thị

admin.site.register(MegaMenu, MegaMenuAdmin) # Đăng kí để hiển thị quản trị
admin.site.register(MenuItem, MenuItemAdmin) # Đăng kí để hiển thị quản trị
```


# Các lưu ý về models
## Phần 1
### Fields
#### Các fields
#### Tham số trong fields

### Quan hệ (Relationship)
#### many-to-one
```
from django.db import models
 
class Manufacturer(models.Models):
    #...
    pass
 
class Car(models.Model):
    manufacturer = models.ForeignKey(Manufacturer, on_delete=models.CASCADE)
    #...
```
#### many-to-many 
```
from django.db import models
 
class Topping(models.Model):
    #...
    pass
 
class Pizza(models.Model):
    #...
    toppings = models.ManyToManyField(Topping)
```

Thuộc tính của một mối quan hệ many-to-many
```
from django.db import models
 
class Person(models.Model):
    name = models.CharField(max_length=128)
 
class Group(models.Model):
    name = models.CharField(max_length=128)
    members = models.ManyToManyField(Person, through='Membership')
 
class Membership(models.Model):
    person = models.ForeignKey(Person, on_delete=models.CASCADE)
    group = models.ForeignKey(Group, on_delete=models.CASCADE)
    date_joined = models.DateField()
    invite_reason = models.CharField(max_length=64)
```
#### one-to-one


## Phần 2
#### Metadata (siêu dữ liệu)
Các siêu dữ liệu sẽ được lưu trong một lớp nội có tên là Meta. Lớp này lưu trữ các thông tin cấu hình model, chẳng hạn như ordering tức là sắp xếp dữ liệu được trả về mặc định theo cột nào, db_table là tên bảng được tạo trong CSDL… [Document](https://docs.djangoproject.com/en/4.2/ref/models/options/)