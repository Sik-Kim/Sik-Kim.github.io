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

# 장고 프로젝트를 위한 환경 세팅
장고 프로젝트 시작까지 환경 세팅 해줘야 할게 너무 많다.


## 환경 세팅

### miniconda 가상환경 설치
지난번 글을 참조하면 된다.
[https://sik-kim.github.io/miniconda/virtual_env/](https://sik-kim.github.io/miniconda/virtual_env/)

### 모듈 설치
**장고 설치**
> $ pip install django


> $ pip install django-cors-headers

**MySQL server에 접속하기 위한 package**
> $ pip install mysqlclient