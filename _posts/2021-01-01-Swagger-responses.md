---
title: "Django ⎜ 여러 Serializer가 있을 때 swagger의 responses 만들기"
date: 2021-01-01 01:25:00 -0400
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

## Swagger는 왜 좋은가.
프론트와 백엔드간의 통신을 구축하기 위해서 여러 커뮤니케이션이 오고 갈 것이다. 이 API는 어떻게 구축할 것이며 엔드포인트는 무엇이며... 얘기하고 맞춰야할게 한두개가 아니다. API 구축이 끝나도 Endpoint 주소, data 형식, key값 등을 또 디테일하게 논의해야 한다. 
이렇게 복잡한 소통을 쉽게 하자고 작성하는 것이 API Documentation 일 것이다. 그리고 API Documentation을 code화 한 것이 Swagger 이다.  

**Swagger를 사용하면 문서 마저도 code로 대체 할 수 있고 실제 code와 문서가 연동되기 때문에 정확하고 빠르게 프론트와 소통할 수 있다.**
Swagger를 사용하여 화면을 띄우기까지는 크게 어렵지 않았다.  
**다만, Swagger 기능 중 responses 값을 원하는대로 가져오는 것이 까다로워 정리하려 한다.**

<br>

## Swagger의 responses
swagger에 responses는 해당 API의 정보의 상세내용을 보여준다. 이걸 통해 response되는 데이터의 key값, 형식 등을 프론트에서도 확인 할 수 있고 협의할 때 데이터로 활용하기도 좋다. swagger의 resposnes 형식은 아래와 같다.

![swagger_responses](https://i.ibb.co/fDxhN05/image.png)

<br>

## Swagger의 responses값에 Serializer가 여러개 있을 때?
Serializer가 1개만 response되는 view에서는 크게 어려울 것이 없다. responses 값에 그냥 적어주면 되기 때문이다.  
하지만 아래 view와 같이 여러 Serializer가 response되는 경우에서는 responses 작성이 조금 까다로워 진다. 방법 2가지를 살펴보자.


### 방법 1 - responses용 Serializer 생성하기
serializers - 1, views - 1 과 같이 MapSearchResponseSerializer를 만들어서 view에 적용해준다. 이게 가장 간결하고 좋은 방법이라고 생각한다.  
(지난번 포스팅에서도 그렇고 Serializer를 구축해서 뭔가를 할 수 있다면 난 그 방법이 좋다고 생각한다.)
[https://sik-kim.github.io/django/SerializerMethodField/](https://sik-kim.github.io/django/SerializerMethodField/)

```python
# in serializers - 1

class SearchQuerySerializer(serializers.Serializer):
    search = serializers.CharField(help_text="검색어", required=False)

class MapSearchResponseSerializer(serializers.Serializer):
    brands        = SearchSerializer(many=True)
    neighborhoods = NeighborhoodSerializer(many=True)
    subways       = SubwaySerializer(many=True)
```

```python
# in views - 1

class MapSearchView(APIView):
    @swagger_auto_schema(
        operation_description='검색 키워드 입력시 리스트 보여주기',
        query_serializer=SearchQuerySerializer,
        tags=['Map'],
        responses={200: MapSearchResponseSerializer()}
    def get(self, request):
        search = request.GET.get('search', None)

        (~중략~)

        serializer_brands        = SearchSerializer(brands, many=True)
        serializer_neighborhoods = NeighborhoodSerializer(neighborhoods, many=True)
        serializer_subways       = SubwaySerializer(subways, many=True)

        return JsonResponse(MapSearchResponseSerializer({
            "brands"        : serializer_brands.data,
            "neighborhoods" : serializer_neighborhoods.data,
            "subways"       : serializer_subways.data
            }).data, status=status.HTTP_200_OK)
```

<br>

### 방법 2 - responses를 작성할 때 Serializer를 더하는 방법
responses를 위한 Serializer를 만들지 않고도 가능은 하다. 다만 좀 복잡한데 아래 views - 2와 같이 데이터 양식을 직접 작성해주는 방법도 있다. 위에 했던 responses를 위한 Serializer를 사용하는 방법이 통하지 않을 때 활용해 볼 수 있을 것이다. 그러나 코드도 길어지고 복잡해서 별로 사용하고 싶은 방법은 아닌듯 하다. 무엇보다도 코드 가독성이 높은게 난 좋기 때문이다.



```python
# in serializers - 2

class SearchQuerySerializer(serializers.Serializer):
    search = serializers.CharField(help_text="검색어", required=False)
```

```python
# in views - 2
class MapSearchView(APIView):
    @swagger_auto_schema(
        operation_description='검색 키워드 입력시 리스트 보여주기',
        query_serializer=SearchQuerySerializer,
        tags=['Map'],
        responses={
            200: openapi.Schema(
                type=openapi.TYPE_OBJECT,
                properties = {
                    "brands":openapi.Schema(
                        type=openapi.TYPE_ARRAY,
                        items=openapi.Schema(
                            properties = {
                                **SchemaGenerator.get_field_properties(SearchSerializer),
                            },
                            type=openapi.TYPE_OBJECT
                        )
                    ),
                    "neighborhoods":openapi.Schema(
                        type=openapi.TYPE_ARRAY,
                        items=openapi.Schema(
                            properties = {
                                **SchemaGenerator.get_field_properties(NeighborhoodSerializer),
                            },
                            type=openapi.TYPE_OBJECT
                        )
                    ),
                    "subways":openapi.Schema(
                        type=openapi.TYPE_ARRAY,
                        items=openapi.Schema(
                            properties = {
                                **SchemaGenerator.get_field_properties(SubwaySerializer),
                            },
                            type=openapi.TYPE_OBJECT
                        )
                    ),
                }
            )
        }
    )
    def get(self, request):
        search = request.GET.get('search', None)

        (~중략~)

        serializer_brands        = SearchSerializer(brands, many=True)
        serializer_neighborhoods = NeighborhoodSerializer(neighborhoods, many=True)
        serializer_subways       = SubwaySerializer(subways, many=True)

        return JsonResponse({
            "brands"        : serializer_brands.data,
            "neighborhoods" : serializer_neighborhoods.data,
            "subways"       : serializer_subways.data
            }, status=status.HTTP_200_OK)
```