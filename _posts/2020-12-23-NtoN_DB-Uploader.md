---
title: "Python ⎜ ManyToMany값 csv로 database 입력하기"
date: 2020-12-24 01:25:00 -0400
summary: 
toc : ture
toc_sticky: true
comments: true
tag : 
    - python
    - django
category : 
    - python
---

### ManyToMany값을 넣기 위한 db_uploader는 어떻게 작성하면 될까?

<br>

User와 Brand라는 모델이 ManyToMany로 연결되어 있다고 하자.  
아래 models의 코드와 같이 작성해주면 database에서는 users_favor라는 이름의 table이 자동으로 생긴다. (자동으로 table명을 지정해준다. 물론 직접 네이밍을 할 수도 있다.)  

다대다 중간테이블을 위한 model을 models.py에서 직접 작성해줘도 되고 안해줘도 상관없다.


<br>

**예제 코드**

```python
# in models.py
class User(models.Model):
    favor         = models.ManyToManyField('brand.Brand', related_name='favors')
```

```python
# in db_uploader.py
import os
import django
import csv
import sys

def insert_users_favor():
        CSV_PATH_PRODUCTS = 'csv/users_favor.csv'
        with open(CSV_PATH_PRODUCTS) as in_file:
                data_reader = csv.reader(in_file)
                next(data_reader, None)
                for row in data_reader:
                        user = User.objects.get(id=row[0])
                        brand = Brand.objects.get(id=row[1])
                        user.favor.add(brand)     # point
```

<br>

**users_favor csv 파일**   
아래와 같이 csv를 작성해서 db_uploader를 실행하면 database에 값이 입력된다. 이와 같이 참조 필드는 id값으로 작성해도 되고 값으로 실제 값으로 입력해줄 수도 있다.  
경우에 따라 db_uploader 코드가 조금씩 달라진다.

![csv](https://i.ibb.co/p2G6ZWb/image.png)