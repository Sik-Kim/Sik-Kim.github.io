---
title: "Django ⎜ Swagger의 parameters(query string)값 만드는 2가지 방법"
date: 2021-01-01 01:50:00 -0400
summary: 
toc : ture
toc_sticky: true
comments: true
tag : 
    - python
    - django
    - DRF
    - Serializer
    - Swagger
category : 
    - swagger
---

## Intro
프론트와 data를 주고 받을 때 parameters값(query string)에 대한 정보는 아주 중요하다.  
Swagger를 통해 parameter에 대한 데이터를 프론트에 알려줄 수 있고 request를 해 볼 수도 있다.

<br>

## Swagger의 parameters 작성 방법

### 방법 1 - parameters를 위한 Serializer 생성
우선 parameters를 위한 Serializer를 작성하는 방법이 있다. Serializer를 작성해주면 view의 code가 깔끔해지는 장점이 있다.  
아래와 같이 help_text로 설명을 적어줄 수 있고 필수인지 선택인지 구분도 가능하다.  
다만, models에서 작성했던 필드를 중복으로 작업하는 느낌이 없지는 않다.

```python
# in serializers - 1

class MapQuerySerializer(serializers.Serializer):
        lat1 = serializers.DecimalField(help_text="latitude 작은 값", required=True, max_digits=20, decimal_places=15)
        lat2 = serializers.DecimalField(help_text="latitude 큰 값", required=True, max_digits=20, decimal_places=15)
        log1 = serializers.DecimalField(help_text="logitude 작은 값", required=True, max_digits=20, decimal_places=15)
        log2 = serializers.DecimalField(help_text="longitude 큰 값", required=True, max_digits=20, decimal_places=15)
        category1 = serializers.CharField(help_text="카테고리 이름1", required=False)
        category2 = serializers.CharField(help_text="카테고리 이름2", required=False)
        category3 = serializers.CharField(help_text="카테고리 이름3", required=False)
        is_recruitment = serializers.CharField(help_text="가맹점 모집 유무", required=False)
        search = serializers.CharField(help_text="입력한 검색어", required=False)
        favor_category = serializers.CharField(help_text="관심브랜드 클릭", required=False)
        brand_id = serializers.IntegerField(help_text="브랜드리스트 중 1개클릭", required=False)
```

```python
# in views - 1

class MapView(APIView):
    @swagger_auto_schema(
        operation_description='지도화면 + 브랜드리스트 + 검색 + 필터 API',
        query_serializer=SearchQuerySerializer,    # point
        responses={200: MapBranchSerializer(many=True)},
        tags=['Map']
    )
    def get(self, request):
    
    (~중략~)

```

<br>

**swagger 화면**

![swagger-parameters](https://i.ibb.co/0ZZ5CrX/image.png)


<br>

### 방법 2 - swagger parameters 작성시 입력
방법1과 같이 따로 Serializer를 작성하지 않고 아래와 같이 바로 swagger를 작성하는 방법도 있다. 어떤 방법이 좋을지는 코드 상황과 본인 취향에 따라 다른 것 같다.  
좀 더 정확한 데이터를 담을 수 있는 건 방법 1인것 같고 더 간결하고 쉬운 방법은 방법 2인 것 같다.  
(개인적으론 Serializer를 생성하더라도 view가 깔끔해지고 더 디테일한 정보를 줄 수 있는 방법 1이 내 취향인 것 같다...)

```python
# in views

class MapView(APIView):
    @swagger_auto_schema(
        operation_description='지도화면 + 브랜드리스트 + 검색 + 필터 API',
        manual_parameters=[                                           # point
            openapi.Parameter('lat1', openapi.IN_QUERY, type=openapi.TYPE_STRING),                
            openapi.Parameter('lat2', openapi.IN_QUERY, type=openapi.TYPE_STRING),                
            openapi.Parameter('log1', openapi.IN_QUERY, type=openapi.TYPE_STRING),                
            openapi.Parameter('log2', openapi.IN_QUERY, type=openapi.TYPE_STRING),                
            openapi.Parameter('category1', openapi.IN_QUERY, type=openapi.TYPE_STRING),                
        ],  
        responses={200: MapBranchSerializer(many=True)},
        tags=['Map']
    )
    def get(self, request):

    (~중략~)

```

**swagger 화면**  

![swagger_parameters](https://i.ibb.co/LvBNSJC/image.png)