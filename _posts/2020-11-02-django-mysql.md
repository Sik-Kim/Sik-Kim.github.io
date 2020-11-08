---
title: "Django Starbucks Page Clone ⎜ models.py 작성"
date: 2020-11-02 07:52:00 -0400
summary: 이 summary는 화면 어디서 나오지???
toc : ture
toc_sticky: true
comments: true
tag : 
    - django
    - python
    - mysql
category : 
    - django
---

# 실습 내용

django와 mysql을 연동하고 실제로 table을 만들고 data까지 넣어보도록 하겠다. 지난번 ERD 모델링을 만들었던 starbucks 페이지로 실습을 하려한다.  
[https://sik-kim.github.io/database/database/](https://sik-kim.github.io/database/database/)

<br><br>

# 환경 세팅
개발의 시작은 환경 세팅이 아니던가. 하나씩 해보도록 하자.


## 가상환경 설치(miniconda 사용)
지난번 가상환경 관련 포스팅을 참조하면 된다.  
[https://sik-kim.github.io/miniconda/virtual_env/](https://sik-kim.github.io/miniconda/virtual_env/)

## 모듈 설치

**장고 설치**
> $ pip install django

**장고 cors 설정**
> $ pip install django-cors-headers

**MySQL server에 접속하기 위한 package(mysql 설치 후 설치할 것)**
> $ pip install mysqlclient

<br><br>

## 장고 프로젝트 생성
> $ django-admin startproject westarbucks  
> $ cd westarbucks

## 앱 생성(앱 이름 : products)
> $ python manage.py startapp products

<br>

# 장고 Setting 및 Database 연동

## setting.py 설정

### IP 허용
```python
ALLOWED_HOSTS = ['*']
```

### APP 설정
![setting.py_apps](https://i.ibb.co/5kbmnQM/settings-py-apps.png)

### westarbucks/urls.py 수정
```python
from django.urls import path

urlpatterns = [
]
```

<br>

## Database 세팅 

### mysql server 시작
> $ mysql.server start  

### mysql database root 권한으로 실행
> $ mysql -u root -p  

### database 생성 (mysql명 : starbucks_db)
> mysql> create database starbucks_db character set utf8mb4 collate utf8mb4_general_ci;

### database 연동 (setting.py 에 작성)
```python
DATABASES = {
    'default' : {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'DATABASE 명',
        'USER': 'root',
        'PASSWORD': 'DB접속용 비밀번호',
        'HOST': '127.0.0.1',
        'PORT': '3306'
    }
}
```

<br><br>

자.. 드디어 모든 환경 세팅이 끝났다...

<br>

![zzal_start](https://i.ibb.co/6XZZb3v/zzal-start.jpg)

<br>

# Django - models.py 작성
models.py는 장고와 database(mysql)간의 연동을 가능하게 해준다. **models.py에서 작성한 코드가 database table의 뼈대가 되는 것이다.** table의 칼럼명, 자료형, 제한사항, PK, FK 등에 대한 정보를 작성을 하게된다.

```python
from django.db import models

# Create your models here.


class Menu(models.Model):
		name = models.CharField(max_length=20)


class Category(models.Model):
		name = models.CharField(max_length=20)
		menu = models.ForeignKey('Menu', on_delete=models.CASCADE)


class Product(models.Model):
		name  = models.CharField(max_length=100)
		english_name = models.CharField(max_length=100, default="")
		description = models.CharField(max_length=100, default="")
		category = models.ForeignKey('Category', on_delete=models.CASCADE)

class Nutritions(models.Model):
        one_serving_kca = models.DecimalField(max_digits=10, decimal_places=2, blank=True, null=True)
        sodium_mg = models.DecimalField(max_digits=10, decimal_places=2, blank=True, null=True)
        saturated_fat_g = models.DecimalField(max_digits=10, decimal_places=2, blank=True, null=True)
        sugars_g = models.DecimalField(max_digits=10, decimal_places=2, blank=True, null=True)
        protein_g = models.DecimalField(max_digits=10, decimal_places=2, blank=True, null=True)
        caffeine_mg = models.DecimalField(max_digits=10, decimal_places=2, blank=True, null=True)
        product = models.ForeignKey('Product', on_delete=models.CASCADE, default="")

class Images(models.Model):
        image_url = models.CharField(max_length=1000)
        product = models.ForeignKey('Product', on_delete=models.CASCADE)

class Sizes(models.Model):
        name = models.CharField(max_length=20, blank=True, null=True)
        size_ml = models.CharField(max_length=20, blank=True, null=True)
        size_fluid_ounce = models.CharField(max_length=20, blank=True, null=True) 
        nutrition = models.ForeignKey('Nutritions',on_delete=models.CASCADE)

class Allergy(models.Model):
        name = models.CharField(max_length=20, blank=True, null=True)

class Allergy_Product(models.Model):
        product = models.ForeignKey('Product', on_delete=models.CASCADE)
        allergy = models.ForeignKey('Allergy', on_delete=models.CASCADE)

```
<br><br>

# python shell을 통한 database에 data 입력
models.py에 작성한 코드로 database table의 뼈대(column명)은 구축했다. 이제는 table 속성 안에 들어갈 실제 data를 넣어보자.  
python shell을 통해 직접 넣어 볼 수 있다.
(혼자 공부할때나 이렇게 data를 CRUD 하는 것이지 실제 프로젝트에서는 이런식으로 할일이 없으니 data 안 날려먹게 유의하자!)

## python shell 진입
> python manage.py shell  


## models.py에 작성한 Class import
python shell에 진입할 때마다 필요한 Class를 import 해줘야 한다. (python shell에 진입할 때 마다 import 해주는 것을 잊지 말자.)
> from products.models import Menu, Category, Product, Nutritions


## table 작성

<br>

### Menu table에 Data 입력

**create() 사용**  
> Menu.objects.create(name="음료")

**save() 사용**  
> a3 = Menu(name="상품")
> a3.save()

**bulk_create()**
> a1 = Men(name="음료")  
> a2 = Menu(name="푸드")  
> Menu.objects.bulk_create([a1, a2])

### Nutritions table에 Data 입력

**참조값이 있을 시 임시로 쓸 변수(a1)에 참조하는 Model(Product)의 인스턴스를 넣는다.**

> a1 = Product.objects.get(name="나이트로 바닐라 크림")  

**위에서 지정한 a1 인스턴스와 함께 Nutritions data값을 만든다.**
> Nutritions.objects.create(one_serving_kca=75, sodium_mg = 20, saturated_fat_g = 2, sugars_g = 10, protein_g = 1, caffeine_mg = 245, product = a1)

### Images table에 Data 입력

a1 = Product.objects.get(name="나이트로 바닐라 크림")

Images.objects.create(image_url="[https://www.starbucks.co.kr/menu/drink_view.do?product_cd=9200000002487](https://www.starbucks.co.kr/menu/drink_view.do?product_cd=9200000002487)", product = a1)


### Size table에 Data 입력

a1 = Nutritions.objects.get(id=1)

Sizes.objects.create(name="Tall(톨)", size_ml="355ml", size_fluid_ounce="12 fl oz", nutrition = a1)


**Allergy table에 Data 입력**



Allergy.objects.create(name="우유")

**Allergy_product table에 Data 입력(N:N Table의 가운데 table)**



a1 = Product.objects.get(id=1)

b1 = Allergy.objects.get(id=1)

Allergy_Product.objects.create(product=a1, allergy=b1)

<br><br>

# 결과 확인

![starbucks_table](https://i.ibb.co/MPMVJBQ/starbucks-table.jpg)

~~캡쳐하면 될 것을 사진을 찍었네..~~

<br><br>

# 보안해야 할 점
- QuerySet 작성 연습
- Model Field 사용
- Django Documentation과 친해지기
