---
title: "Django ⎜ 직방 클론 프로젝트 후기"
date: 2020-12-12 20:25:00 -0400
summary: 
toc : ture
toc_sticky: true
comments: true
tag : 
    - django
    - 클론 프로젝트(직방)
category : 
    - django
---

# Project Overview
지도와 부동산 관련 사이트를 정말 만들어보고 싶었다. 프로젝트 선정 때 난 호갱노노와 직방 2개만 제출했었다. 가장 하고 싶었던 호갱노노는 하지 못했지만, 직방이 동기들의 많은 득표를 얻었고 내가 PM이 되어 프로젝트를 시작했다.  
그런데 막상 하고 싶은 프로젝트를 시작했는데 어떻게 해야 할지 막막했다. 1차 프로젝트를 진행하며 이커머스와 같은 일반적인 사이트에 대해서는 이해력이 높아졌는데 지도로 이루어진 직방은 새로운 챌린지가 되었다. 또다시 1차 프로젝트를 하는 기분이었다.

<br>

# Project 소개

### 작업 기간
2020.11.30 ~ 2020.12.11


### 기술 스택

### 공통
HTTP  
Git

### 프론트엔드 (3명)
HTML/CSS
JavaScript  
React  
SASS  

### 백엔드 (3명)
Python  
Django  
MySQL  
AWS EC2, RDS  
Gunicorn  
RestFul API  
PyJWT, Bcrypt  
Beautiful soup, Selenium  
Docker  

### Tool
VSC, Vim  
Aquerytool  
Postman  
Trello  
Slack  


### 엔드 포인트 (총 8개)
1. 아파트 지도 위치와 줌에 따른 화면 구현(+필터)
2. 아파트 디테일 화면 구현
3. **아파트 가격 그래프 구현 (주요 담당)**
4. 원룸 지도 위치와 줌에 따른 화면 구현(+필터) 및 디테일 화면
5. **아파트, 원룸 주변 편의시설 찾기 지도 구현 (주요 담당)**
6. **아파트, 원룸 검색 기능 구현 (주요 담당)**
7. 유저 카카오 소셜로그인
8. 유저 구글 소셜로그인

<br>

# What did I do?
### 기능 구현
- Django를 사용하여 앱 코드 구현
- 아파트 기간/평수/거래 타입 별 가격, 거래수 변동 그래프 API 구현(직방 기능)
- Python haversine 모듈을 활용하여 위치정보 기반 주변 시설 필터링 API 구현(다방 기능)
- 부동산 검색 API 구현(직방 기능)
- unittest.mock을 활용한 API 별 Unit Test 구현

### 프로젝트 관리
- Git, git branch, git rebase, Github를 통한 형상 관리
- Restful API 규칙에 따른 End-Point 구현
- AWS EC2, RDS를 사용하여 서버 배포
- Docker를 활용한 가상화 시스템 적용

### 데이터베이스
- Aquery Tool을 사용하여 ERD Modeling 구축
- Python csv 모듈을 활용한 Database 구축 및 MySQL 활용
- Beautiful Soup, Selenium을 사용하여 부동산좌표 데이터 크롤링

### 협업 방식
- 직방 클론 프로젝트 PM을 담당하며 프로젝트 관리 및 팀원들과의 협업 경험
- Agile 방법론 경험 및 Sprint 기간을 1주로 설정하여 프로젝트 관리
- Trello, Slack을 통한 프로젝트 관리 및 팀원들과의 소통
- API Documentation을 활용하여 프론트단과 end-point 및 key 값 등의 업무 협업


<br>

# Project Review

### 1. ERD Modeling
ERD Modeling URL : https://aquerytool.com:443/aquerymain/index/?rurl=6dcce6e9-95f0-4f75-b71c-4232ac16e0cd& (Password : rawjqt)

백엔드의 시작은 모델을 정의하고 관계를 지정하는 ERD Modeling인게 확실하다. 모호하기만 했던 지도 프로젝트도 ERD Modeling을 작성하고 나니 한 줄기 빛이 보였다. 아.. 이렇게 하면 되는구나.  
지도 API를 이용해서 지도를 사용했을 뿐이지 개발에 있어서 본질은 이커머스와 크게 다를 게 없었다. 모델 구조를 파악하고, API 기능을 나누고, 데이터를 구축하고...  
2차 프로젝트를 하며 더더욱 어떤 웹사이트든지 시간이 오래 걸릴지는 몰라도 어떻게든 할 수는 있겠구나 생각이 들었다.

**모델링 작성 중 고민한 내용**  
- Apartment와 Room 특성이 비슷해서 모델을 합칠 수 있을까 고민도 했었다. 하지만 알고보니 비슷해 보이지만 완전히 달라서 무조건 모델을 나누는 게 맞았다.
- Facility app(보라색)은 다른 App과의 관계가 연결되어 있지 않다. 관계가 없을 수도 있나? 처음에 되게 이상했는데 이런 경우도 있을 수 있는 걸 알게 됐다.

![erd modeling](https://i.ibb.co/njdv301/modeling.png)

<br>

### 2. API

### 1) 아파트 Graph API
처음엔 그냥 아파트 가격 데이터 조금 정리해서 프론트로 전달해주면 되겠구나 쉽게 생각했다. 하지만 데이터를 추출하기까지 필터링이 너무 많았다.  
아파트단지별, 평수별, 년/월별, 거래타입별로 나뉜 데이터를 또다시 연 단위로 묶은 평균가와 거래 수량을 필터링하는 게 쉽지 않았다.
django의 annotate를 활용해서 아래와 같이 code를 작성했다.

![graph](https://i.ibb.co/DbKJxtx/graph.png)

**작성 Code (매매시세 부분)**
```python
class ApartmentGraphView(View):
    def get(self, request, id):
        try:
            size_id    = request.GET.get('size_id', None)
            apartments = ApartmentComplex.objects.get(id=id).apartment_set.\
            values(
                'trade_year',
                'trade_month',
                'size_id',
                'trade_type_id'
                ).\
                annotate(
                    Avg('price'),
                    Count('price')
                    )
            trade_filter     = {'trade_type_id':3, 'size_id':size_id}
            trade_apartments = apartments.filter(**trade_filter)
            trade_years = trade_apartments.values('trade_year').distinct()

            trade_apartments_data = [{
                year['trade_year'] : [{
                    "date"   : str(row['trade_year'])+'. ' + str(row['trade_month']),
                    "values" : [int(row['price__avg']), row['price__count']]
                            } for row in trade_apartments if row['trade_year'] == year['trade_year']]
                            } for year in trade_years]


```

<br>

### 2) 부동산 주변 편의시설 찾기 API
이건 다방에 있는 기능인데 재밌을 것 같아 추가했다.
아파트든 원룸이든 특정 부동산의 디테일을 선택했을 때 주변의 편의시설(지하철,학교,편의점,카페)를 찾아주는 기능이다.
선택된 부동산의 좌표와 python의 haversine 모듈을 이용해 금방의 데이터들을 불러왔다.  
(haversine 모듈은 2개의 좌표값을 입력하면 거리를 계산해주는 모듈이다.)

편의시설 데이터 불러오는 속도를 높이기 위해서 먼저 limit으로 범위를 설정한 다음 haversine으로 데이터를 불러왔다.
아래 코드는 5km 내에 있는 학교를 찾는 코드를 작성한 것이다.

![facility_map](https://i.ibb.co/n7Cq9DX/facility-map.png)

```python
            latitude = float(location.latitude)
            longitude = float(location.longitude)
            position      = (latitude,longitude)
            LATITUDE_1KM  = 0.00904
            LONGITUDE_1KM = 0.00898
            if not (-90 < latitude < 90 and -180 < longitude < 180):
                return JsonResponse({"message":"INVALID_COORDINATE"}, status=400)
            limit = (
                Q(latitude__gt=latitude-5*LATITUDE_1KM, latitude__lt=latitude+5*LATITUDE_1KM) &
                Q(longitude__gt=longitude-5*LONGITUDE_1KM, longitude__lt=longitude+5*LONGITUDE_1KM)
            )

            schools = School.objects.filter(limit)
            near_schools = [school for school in schools if haversine(position, (school.latitude, school.longitude)) < 5 ]
            school_data = [{
                "id"        : school.id,
                "name"      : school.name,
                "latitude"  : school.latitude,
                "longitude" : school.longitude,
                "distance"  : int(haversine(position, (school.latitude, school.longitude), unit='m'))
            }for school in near_schools]
```

<br>

### 3) 검색 API
지도에서의 필터/검색 기능은 이커머스와는 좀 다르다. 가장 큰 차이점은 지도는 좌표에 따라서 데이터를 출력해준다는 점이다. 검색은 그 좌표를 찾기 위함이고 필터는 좌표에 따른 반환할 데이터를 걸러주는 역할 정도이다.
처음에 이러한 특징을 잘 몰랐을 때는 이커머스와 같이 검색과 필터를 합쳐야 하나 고민을 했었고 형편없는 그림 실력이나 직접 손으로 그려보면서 이해가 되기 시작했다.

- 직방    : 검색/필터 **+ 좌표** -> 정보  
- 이커머스 : 검색/필터 -> 정보

![search-drawing](https://i.ibb.co/XXGT1Kv/search-drawing.png)

<br>

### 4) Unit Test
유닛테스트 작성은 또 다른 난관이었다. 처음 해보기도 하고 문법도 처음엔 낯설었다.  
그래도 처음이 어렵지 비슷한 패턴으로 작성되기에 나중에는 수월하게 작성할 수 있었다. 첫 유닛테스트를 작성할 땐 에러 100번은 내고 성공할 수 있었다.

**Unit Test 관련 내가 실수한 사항**
- 좌표값을 그냥 소수점으로 써주니 부동소수점 에러가 계속 발생(str로 변환)
- assertEquals(response.json(), {}) 에서 중갈호를 잘못 작성
- id값 불일치
- 모델 create 순서

```python
class SearchTest(TestCase):

    maxDiff = None

    def setUp(self):
        client = Client()
        
        ApartmentComplex.objects.create(
            id               = 99,
            name             = '위코드아파트',
            household_number = 1234,
            completion_year  = 2020,
            address          = '서울시 위코드동',
            latitude         = str('37.518740000000001'),
            longitude        = str('127.020550000000001')
        )

        Neighborhood.objects.create(
            id        = 1,
            name      = "위코드구",
            latitude  = str('37.523433300000005'),
            longitude = str('127.024074900000006')
        )

    def tearDown(self):
        ApartmentComplex.objects.all().delete()
        Neighborhood.objects.all().delete()

    def test_getting_search_lists_success(self):
        client   = Client()
        response = client.get('/apartment/search?search=위코드')
        self.assertEquals(response.json(),{
            "lists": [
                {
                    "name"     : "위코드아파트",
                    "latitude" : str('37.518740000000001'),
                    "longitude": str('127.020550000000001'),
                    "address"  : "서울시 위코드동"
                },
                {
                    "name"     : "위코드구",
                    "latitude" : str('37.523433300000005'),
                    "longitude": str('127.024074900000006')
                }
            ]
        }
        )
        self.assertEqual(response.status_code, 200)

    def test_getting_search_lists_fail(self):
        client   = Client()
        response = client.get('/apartment/search?search=위코드2호점')
        self.assertEqual(response.json(),
        {
            "message":"NO_RESULT"
        }
        )
        self.assertEqual(response.status_code, 400)

    def test_getting_search_lists_not_found(self):
        client   = Client()
        response = client.get('/apartment/search/50')
        self.assertEqual(response.status_code, 404)
```

<br>

### Trello
PM이라 그런지 이번 프로젝트는 1차보다도 Trello에 신경을 썼다.
지금 벅찬 건 맞는 거 같은데 이게 어느 정도 인지 계속 감을 잡아야 했다.  
모든 계획을 완료하지는 못했다. 2주차 월요일에 진행한 2차 스프린트 회의에서 많은 걸 비워내고 계획을 재정립했다.
그럼에도 반드시 하고 싶은 건 프로젝트 발표 이후에 하기로 했다.
결국, 백엔드에서는 Docker, API Documentation 등을 발표 이후로 하기로 했다.

![trello](https://i.ibb.co/J7SC521/trello.png)

<br>

# Project 후기

### PM으로서의 경험
PM이 되었을 때 뭐 별거 있겠어 생각했지만 긴장되고 불안한 마음이 아예 없진 않았다.  
내가 모두를 아우를 만큼 뛰어난 실력도 아니고 그토록 직방을 하고 싶었으나 그럴싸한 계획이 있는 것도 아니었기 때문이다.  

그래도 나만의 팀 운영 계획이라면 있었다. 팀원들 모두가 스스로 잘하게끔 하는 것.  
스탠딩 미팅과 스프린트 미팅을 통해 팀원들의 생각을 계속해서 끄집어내고 괜찮으면 바로 진행했다. 

그리고 가장 중요시 생각한 것은 팀원들을 믿는 것이었다.

물론 잘 진행되고 있는지 계속해서 확인했고 논의사항이 있으면 즉각 해결하려고 했다.  
많은 시행착오가 있었지만, 마지막까지 트러블 없이 재밌게 진행할 수 있었다.

<br>

### 프론트와 백엔드의 소통
1차 프로젝트와 비교해서 프론트와 협의할 사항이 훨씬 많았다.  
좌표값을 누가 어떻게 줄지부터 아파트 그래프의 복잡한 데이터는 어떤 형식으로 보낼지 등. 프-백간의 소통을 제대로 하지 않았으면 큰일 났겠구나 싶은 생각도 들었다.  
또한 백엔드도 프론트 아는 게 필수적이란 생각이 들었다.

<br>

### 프로젝트 발표 전 난리의 현장
발표준비를 끝마쳤는데 발표 3분 전 갑자기 아파트 지도 화면이 열리지 않는 것이었다. 응?
팀원들 모두가 그야말로 멘붕에 빠졌다.
![menbung](https://i.ibb.co/9ThWVTk/menbung.png)

양해를 구하고 발표를 한 차례 밀고 모두가 필사적으로 에러 찾기에 나섰다. 
에러 내용은 Connection Timeout 이였다.

wifi 문젠가 싶어서 필요한 컴퓨터 외 wifi도 다 종료해보기도 하고 데이터가 너무 많아서 그런가 싶어 그 와중에 데이터를 밀고 데이터를 조금만 넣고 다시 해봤지만 안됐다. 뭘 해도 안 됐고 그동안 2주가 이렇게 날아가나 싶어 식은땀이 막 흘렀다.

그러다 프론트의 화면을 유심히 보니 원인을 알게 됐다. 원인은 단순했다. 프론트에서 연결한 엔드포인트 IP가 잘못됐던 것이다. 백엔드 API를 하나의 서버로 합치면서 합친 IP로만 Request해야 하는데 기존대로 각 백엔드 담당자의 서버로 계속해서 Request를 했던 것이다.

우여곡절 끝에 문제를 해결하고 발표는 성공적으로 마쳤다.  
하... 넋이 나간 팀원들의 얼굴. 아마 내 얼굴도 그랬을 것이다. 우리는 환호성을 질렀다.  

10분 사이 참 많은 일이 있었고 또 한차례 성장한 것만 같았다.  
실제 서비스 배포 전 이런 식으로 했다가는 큰일 난다는 큰 교훈을 얻었다.  

그리고 필사적인 팀워크를 발휘해준 팀원들에게 고마웠다.



<br>

### 의욕과 현실 사이의 협상
프로젝트 시작 전에는 의욕이 많이 앞서 직방, 호갱노노, 다방에 있는 매력적인 기능들 모두 해보고 싶었다. 하지만 현실은 API 1개를 테스트와 리팩토링까지 해서 제대로 구현하는 것도 시간이 꽤 오래 걸린다. 그 외에도 데이터 작업, AWS, Docker, Git, 문서작업, 위코드 세션 등 할 일이 많다.  
적당히 과한 의욕은 좋으나 비현실적인 의욕은 나에게도 팀원에게도 위험할 수 있겠다고 생각했다.

<br>

### 개선 또는 궁금한 점
데이터를 10000개 정도 넣고 AWS 서버를 사용하니 너무 느려져서 데이터 수를 줄였다. 그런데 실제 서비스에서는 훨씬 더 많을 데이터를 사용할 텐데 그 많은 양을 어떻게 감당하는지 궁금하다.  

카카오나 네이버 등 지도 API를 사용해보고 싶다. 이번 프로젝트는 온전히 프론트에서만 사용했다.  

유닛 테스트를 패턴화된 방식으로만 작성했는데 더 다양한 작성법을 익히고 싶다.

<br>

### 마무리
프로젝트가 끝나고 집에 오자마자 프로젝트를 떠올리며 이것저것 메모를 해뒀었다. 정신없이 지나간 2주 동안 정말 힘들기도 힘들었고 많이 웃기도 웃었다.  조금이라도 생생할 때 적어놓고 싶었다. 팀으로 함께 했기에 또 한차례 성장한 것 같다. 혼자라면 절대 하지 못했을 일들이다. 부족한 PM과 함께 잘해준 팀원들에게 너무 고맙고 기회가 된다면 다음에 한 번 더 같이 해보고 싶다.