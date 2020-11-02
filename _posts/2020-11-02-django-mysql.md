---
title: "장고 mysql 연동(models.py 작성하여 table 만들기)"
date: 2020-11-02 07:52:00 -0400
summary: 장고 models.py, settings.py 작성
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
django를 사용해서 data를 만들어 mysql에 연동하는 것을 해보도록 하겠다. 지난번 ERD 모델링을 만들었던 starbucks 페이지의 데이터로 실습을 하려한다.
[https://sik-kim.github.io/database/database/](https://sik-kim.github.io/database/database/)

# 환경 세팅
장고 프로젝트 시작까지 환경 세팅 해줘야 할게 너무 많다.


## 가상환경 설치(miniconda 사용)
지난번 글을 참조하면 된다.
[https://sik-kim.github.io/miniconda/virtual_env/](https://sik-kim.github.io/miniconda/virtual_env/)

## 모듈 설치

**장고 설치**
> $ pip install django
**장고 cors 설정**
> $ pip install django-cors-headers
**MySQL server에 접속하기 위한 package(mysql 설치 후 설치할 것)**
> $ pip install mysqlclient


# 장고 세팅

## 장고 프로젝트 생성
> $ django-admin startproject westarbucks
> $ cd westarbucks

## 앱 생성(앱 이름 : products)
> $ python manage.py startapp products


## setting.py 설정
### IP 허용
```python
ALLOWED_HOSTS = ['*']
```

### APP 설정
사진유첨

### westarbucks/urls.py 수정
```python
from django.urls import path

urlpatterns = [
]
```

## Database 생성(mysql명 : starbucks_db)
> $ mysql.server start
> $ mysql -u root -p
> mysql> create database starbucks_db character set utf8mb4 collate utf8mb4_general_ci;

## setting.py 설정 (Database 연동)
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

# models.py 작성
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

# python shell 작성


## python shell 진입
> python manage.py shell

> from products.models import Menu, Category, Product, Nutritions

## models.py에 작성한 Class import
> from products.models import Menu, Category, Product, Nutritions, Images, Sizes, Allergy, Allergy_Product


## table 작성

**Menu table 입력**  

**create() 사용**  
> Menu.objects.create(name="음료")

**save() 사용**  
> a3 = Menu(name="상품")
> a3.save()

**bulk_create()**
>>> a1 = Men(name="음료")
>>> a2 = Menu(name="푸드") 
>>> Menu.objects.bulk_create([a1, a2])

**Nutritions table 입력**

a1 = Product.objects.get(name="나이트로 바닐라 크림")
Nutritions.objects.create(one_serving_kca=75, sodium_mg = 20, saturated_fat_g = 2, sugars_g = 10, protein_g = 1, caffeine_mg = 245, product = a1)

**Images table 입력**

a1 = Product.objects.get(name="나이트로 바닐라 크림")

Images.objects.create(image_url="[https://www.starbucks.co.kr/menu/drink_view.do?product_cd=9200000002487](https://www.starbucks.co.kr/menu/drink_view.do?product_cd=9200000002487)", product = a1)


**Sizes table 입력**

a1 = Nutritions.objects.get(id=1)

Sizes.objects.create(name="Tall(톨)", size_ml="355ml", size_fluid_ounce="12 fl oz", nutrition = a1)


**Allergy table 입력**



Allergy.objects.create(name="우유")

**Allergy_product table 입력(N:N 가운데 table)**



a1 = Product.objects.get(id=1)

b1 = Allergy.objects.get(id=1)

Allergy_Product.objects.create(product=a1, allergy=b1)


# 결과 확인

# 보안해야 할 점