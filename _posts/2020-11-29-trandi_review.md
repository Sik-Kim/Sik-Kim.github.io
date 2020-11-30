---
title: "Django ⎜ 브랜디 클론 프로젝트 후기"
date: 2020-11-29 20:25:00 -0400
summary: 
toc : ture
toc_sticky: true
comments: true
tag : 
    - django
    - 클론 프로젝트(브랜디)
category : 
    - django
---

# Project Overview
위코드에서 진행한 1차 프로젝트인 브랜디 클론 프로젝트가 끝났다. 나로써는 팀원들과 협업하며 함께 진행한 첫 번째 프로젝트로 오래도록 기억될 것 같다. 프로젝트 기간동안 잠도 못자고 매번 어려운일 투성이였지만 그럼에도 웃으며 재밌게 할 수 있었던 건 손발이 잘맞었던 팀원들이 있기 때문이 아닐까...

<br>

# Project 소개

## 작업 기간
2020.11.16 ~ 2020.11.27


## 기술 스택

### 공통
- HTTP
- Git

### 프론트엔드 (2명)
- HTML/CSS
- JavaScript
- React
- SASS

### 백엔드 (3명)
- Python
- Django
- RestFul API
- MySQL
- PyJWT
- Bcrypt
- Selenium
- Beautiful soup
- AWS EC2, RDS

### Tool
- Aquerytool
- Postman
- Trello
- Slack

<br>

## 주요 구현 사항
- Access Token을 활용한 회원 관리
- 로그인, 회원가입, 로그아웃
- 쇼핑몰 메인 페이지 및 상품 리스트 페이지
- 상품 디테일 화면
- 상품 필터링 기능
- 상품 검색 기능
- 상품 좋아요 기능  
- **상품 포토리뷰 기능 (주요 담당)**
- **상품 장바구니 기능 (주요 담당)**

<br>

## 결과 화면(주요 담당 부분에 대한 간략한 설명)

### 1. ERD Modeling
order, orderlist table에 대한 고민이 가장 많았다.

##
![erd](https://i.ibb.co/pPgc6sr/erd-trandi.png)

<br>

아래는 프로젝트와 관련없는 일반적인 order 관련 table modeling이다.  
처음에는 이런식으로 장바구니 테이블(wish_lists)을 주문 테이블(Shopping_Carts, Order_Items)와 별도로 만들어 주려고 했지만 우리는 따로 만들지 않고 하나로 합쳐줬다. 즉, Order, Orderlist 2개 table로 장바구니와 주문 모두 담아냈다. table을 합쳐 table이 간단해지는 대신 views.py 작성은 좀 더 어려워졌다.

<br>

![erd](https://i.ibb.co/tB8cN9s/order-modeling.png)

<br>

### 2. 리뷰 CRUD
- C : 텍스트, 별점, 이미지(선택), 유저정보(선택) 입력 기능
- R : Product Detail View에 포함하여 화면 출력, order_by를 이용하여 최신순 정렬 설정
- U : user 인증 후 일부 내용
- D : user 인증 후 삭제

<br>

![review1](https://i.ibb.co/tQjtSMG/review1.png)

<br>

![review2](https://i.ibb.co/NKG78rs/review2.png)

<br>

### 3. 오더리스트(장바구니) CRUD
- C : 장바구니 선택 시 orderlist(장바구니), order(주문) table을 동시에 생성하는 것이 포인트였다. 그리고 동일한 상품의 장바구니가 있을 때는 수량만 증가 시키고 주문 정보는 결제 전인 주문 정보가 없을 때만 만들어주는 예외처리를 넣어줬다. 배송비 계산 함수를 포함했다.
- R : select_related를 사용하여 쿼리수를 3자리수에서 2개로 줄일 수 있었다.
- U : user, quantity 에러 처리를 해줬고 배송비 계산 함수를 포함했다.
- D : 장바구니가 완전히 비어질 때는 주문정보도 동시에 삭제해줬다. C, U와 마찬가지로 배송비 계산 함수를 포함해줬다.

<br>

![orderlist1](https://i.ibb.co/r3795wp/orderlist1.png)

<br>
<br>

# Project Review

## 체크리스트

## ☑️ 공통 목표

- [ ]  Scrum - 스크럼 진행 방식에 대해서 이해했고 Trello 와 같은 tool 을 활용하여 스크럼 방식 아래 프로젝트 진행할 수 있다. **- O**
- [ ]  Standup Meeting - 매일 아침 미팅을 통해 어제 한 일, 오늘 할 일, blocker 세 가지를 공유하며 팀원들과 미팅을 진행할 수 있다. **- O**
- [ ]  Communication - 팀원들과 소통이 필요한 경우 올바른 방법을 통해 의견을 주고 받으며 조율할 수 있다. **- O**
- [ ]  Git - 기본적인 Flow에 따라 Git을 사용할 수 있으며, brach를 생성하고 올바른 이름과 내용을 commit message를 작성할 수 있다. **- O**
- [ ]  문제 해결 능력 - 모르는 과제를 마주하는 경우 Google 검색, stackoverflow 등을 활용하여 문제를 해결할 수 있다. **- O**
- [ ]  Q&A - 스스로 문제 해결이 잘 안 되는 경우, 혹은 누군가가 도움을 요청하는 경우 동기, 혹은 멘토와 올바른 방법으로 질문과 대답을 주고 받을 수 있다. **- O**

## ☑️ Backend

- [ ]  장고 초기세팅(프로젝트 생성, 앱 생성, MySQL DB연결)을 혼자서 할 수 있다. **- O**
- [ ]  one to one, one to many, many to many 개념을 알고 있다. **- O**
- [ ]  JOIN 기본 개념을 이해하고 있고, 
LEFT JOIN, RIGHT JOIN, INNER JOIN, OUTER JOIN의 차이점들을 이해하고 있다. **- ▵**
- [ ]  요구사항에 맞게 데이터 베이스 모델링 설계를 할 수 있다. **- O**
- [ ]  HTTP 기본 개념 (요청/응답, stateless)를 이해하고 있고 메세지 구조를 이해하고 있다. **- O**
- [ ]  GET, POST 메소드 차이점을 알고, 
프론트에서 넘어오는 데이터를 어떻게 처리해야 하는지 알고 있다. **- O**
- [ ]  쿼리 스트링과 JSON으로 전달되는 데이터를 어떻게 받아서 처리하는지 알고 있다. **- O**
- [ ]  프론트에서 회원가입한 유저정보를 데이터 베이스에 저장 할 수 있다. **- O**
- [ ]  데이터 베이스에 저장된 User정보를 리턴하는 엔드포인트를 구현할 수 있다. **- O**
- [ ]  장고 ORM을 사용하여 DB CRUD(Create, Read, Update, Delete)을 구현 할 수 있다. **- O**
- [ ]  Decorator를 구현 및 엔드포인트에 적용 할 수 있다. **- O**
- [ ]  RESTful API 개념을 이해하고 URL 주소를 RESTful 식으로 구현할 수 있다. **- O**
- [ ]  프론트엔드 개발자와 소통하여 front 와 back을 연결 할 수 있다. **- O**
- [ ]  AWS에서 서버를 생성하여 django를 배포할 수 있다. **- ▵**
- [ ]  장고의 폴더 구조(views.py, urls.py, models.py)를 이해하고 있으며 각 파일의 목적과 용도를 이해하고 있다. **- O**

<br>

## 생각보다 아는게 꽤 있잖아..?


위코드의 체크리스트를 체크 해봤는데 대부분의 항목이 체크되서 놀랬다. 
항목 하나하나 깊게 이해했다고 말하긴 어렵겠지만 프로젝트때 실제로 구현을 해봤고 대략적으로 설명 가능한 수준이면 체크 했다.

아직 미숙하다 느껴 ▵를 준 항목이 2개 있다.  
먼저는 JOIN 개념으로 아직 다 이해하지 못했다. select_related, prefetch_related를 사용해보고 대략 table의 JOIN이 일어나는건 알고있으나 정확하게 이해하지 못했다. 좀 더 공부해야할 부분이다.  
프로젝트 발표가 급박해지며 AWS로 database와 서버 생성까지 했지만 최종 반영하지는 못했다. 다음 프로젝트 때는 프로젝트 중간부터 AWS를 활용해보고 싶다.  

<br>


## 잘한게 있다면?

- 가장 잘한점은 팀워크가 아니였을까.
  
![teamwork](https://i.ibb.co/7rCNnjx/team.png)

- 다른팀에 비해 우리팀은 유독 충돌없고 좋은 분위기로 2주동안 진행되어 왔다. 누군가 우리팀을 실리콘 밸리 스타일이라고도 했는데 그럴 수 있었던 것은 누구하나 자기 주장을 고집하지 않고 서로의 의견을 존중하는게 느껴졌다. 그리고 우리가 강조했던 agile한 진행방식을 잘 지킨 것 같다. 좋은 의견은 곧바로 반영하고 어떤 결과가 좋지 않아도 그에 너무 연연하지 않고 다른 방법을 빠르게 찾으려 노력했다.  
- 두번 째로 잘한 점이 있다면 멘토님들의 권유대로 많은 기능구현보다 코드의 리팩토링에 치중한 점이 아닌가 생각된다. 처음에는 나 또한 많은 기능구현에 욕심이 생겼지만 사실 우리팀은 프론트가 2명, 백엔드가 3명으로 백엔드에서 많은 기능을 만든다고 해도 구현이 안될수가 있었다. 작성한 코드를 리팩토링 해봄으로써 쿼리수를 비교해본 다던지 코드를 깔끔하게 만들어주는 메소드 등에 대해 생각해 볼 수 있었다.
- Restful API에 맞게 endpoint를 구성했고 프론트와의 소통도 좋았다.
- Trello도 남긴 보드 없이 모두 완료했다. (팀원들과 자주 소통하며 계획을 agile하게 변경해 나갔다.)

![trello](https://i.ibb.co/RDHgSCJ/trello-trandi.png)

<br>

## 아쉬운 점은?
- orderlist, order 2개 table을 동시 생성하는 부분에서 트랜잭션을 적용하지 못했다.
- 프로젝트를 진행하다보니 API 문서의 절실함을 느꼈다. 2차 프로젝트에는 꼭 작성해보려고 한다.
- 프로젝트 진행하며 다른팀의 코드를 많이 비교하지 못한점이 아쉽다. 2차 프로젝트까지 하고나서 반드시 다른 프로젝트 코드를 공부해봐야 겠다.
- 배송비 관련 작성한 코드가 화면에 반영되지 않아 아쉬웠다.

<br>

## 기억하고 싶은 코드

### 1. get_or_create
장고 메소드 중 하나로 객체(orderlist)의 db 존재 유무에 따라 원하는 로직을 구현할 수 있다.  
코드 적용 방법
- if/else 없이 get_or_create 사용시: orderlist가 db에 있으면 db에서 가져오고 없으면 생성하는 메소드이다.
- if/else와 함께 사용시: db에 객체가 없을 때가 created가 True이고 db에 객체가 있을 때는 created가 False이다.

```python
orderlist, created = OrderList.objects.get_or_create(
    product=product,
    order=current_order,
    color_id=color_id,
    size_id=size_id
)
if created:
    orderlist.quantity = 0
orderlist.quantity += quantity
orderlist.save()


```

### 2. 삼항 연산자 (일명 한줄 if)
한줄에 if, else를 사용함으로써 값이 없을 때는 None으로 처리할 수 있다.  

```python
    "size" : orderlist.size.name if orderlist.size_id is not None else None,
    "color" : orderlist.color.name if orderlist.color_id is not None else None,
```

### 3. image 크롤링
image 크롤링 때문에 프로젝트 중 고생을 좀 많이 했다. 크롤링에 안되는 이유는 너무나 많았다. 내 코드에 문제가 있기도 했고 사이트가 크롤링을 막은 경우도 있었다. 가장 황당했던 것은 사이트의 selector 주소가 거의 실시간으로 바뀌기도 한다는 점...  
최종적으로 프로젝트에 사용할 이미지를 unsplash라는 저작권 문제가 없는 사이트에서 크롤링을 했다.  
csv_filename을 50번 이상 만들어보고 성공할 수 있었다.

```python
import csv
import time
from bs4 import BeautifulSoup
from selenium import webdriver
from webdriver_manager.chrome import ChromeDriverManager

csv_filename = "unsplash_54.csv"
csv_open = open(csv_filename, "w+", encoding="utf-8")  
csv_writer = csv.writer(csv_open) 
csv_writer.writerow(("title", "tags")) 

driver = webdriver.Chrome(ChromeDriverManager().install())
org_crawling_url = "https://unsplash.com/s/photos/model"
driver.get("https://unsplash.com/s/photos/model")

time.sleep(10)

full_html = driver.page_source
soup = BeautifulSoup(full_html, "html.parser")

titles = soup.select(
    "#app > div > div:nth-child(3) > div:nth-child(3) > div > div:nth-child(1) > div > div > div > div > figure > div > div > div > div > a > div > div > div > img"
)
for title in titles:
    url = title["src"]
    csv_writer.writerow(("", url))
```

<br>

## 마무리
부족한 점은 많겠지만 좋은 사람들과 팀으로 협업해 볼 수 있는 좋은 기회였다. 프로젝트가 끝이 났을 때 진심으로 팀원들의 고생스러움이 고맙게 느껴졌다.  
그래 우리가 그래도 뭔가 만들어 내긴 했구나.  

내가 직접 코드를 짜서 그럴싸한 사이트가 만들어진 것도 뿌듯하기도 하고 신기한 경험이였다. 작고 소중한 나의 코드..

앞으로 나는 어떤 개발자가 될까. 

훌륭한 개발자의 요건은 상당히 많겠지만 먼저 **같이 일하고 싶은 개발자가 되고 싶다.**