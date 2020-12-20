---
title: "Django ⎜ [DRF_TUTORIAL] Serializer vs ModelSerializer"
date: 2020-12-20 20:25:00 -0400
summary: 
toc : ture
toc_sticky: true
comments: true
tag : 
    - python
    - django
    - DRF
category : 
    - django
---

# DRF Tutorial 정리

## DRF(Django REST Framework)
DRF는 Restful API 체계를 강력하게 만들어줄 모듈이다. DRF의 다양한 기능을 사용하면 model을 효율적이고 쉽게 관리하기 쉬워지고 view도 좀 더 간결해진다. 또한 validation, pagination, router 등 여러가지 유용한 기능도 있다.

## DRF Tutorial 개요
DRF Tutorial은 단계는 1부터 6까지 있다.  
동일한 model을 가지고 계속해서 단계를 이어나간다. 뒤로 갈수록 drf의 기능들이 추가되는데 오히려 code는 간결해지고 기능은 풍부해진다.  
꽤 친절하게 설명되어 있으니 하나씩 따라해볼 수 있을 것이다.  
[https://www.django-rest-framework.org/tutorial/1-serialization/](https://www.django-rest-framework.org/tutorial/1-serialization/)

<br>

# Tutorial 1 : Serialization

## Serializer란?
> 직렬화(直列化) 또는 시리얼라이제이션(serialization)은 컴퓨터 과학의 데이터 스토리지 문맥에서 데이터 구조나 오브젝트 상태를 동일하거나 다른 컴퓨터 환경에 저장(이를테면 파일이나 메모리 버퍼에서, 또는 네트워크 연결 링크 간 전송)하고 나중에 재구성할 수 있는 포맷으로 변환하는 과정이다. - 위키백과 -

위키백과가 정의한 뜻인데 솔직히 이해하기 어렵다.  
내가 이해한 직렬화는 특정 언어에서 사용하는 데이터 형태(Django의 QuerySet)을 다른 컴퓨터와 공유할 수 있게 json이나 xml로 변환해주는 과정이다.  
json과 같은 약속된 데이터 형식이 없다면 데이터를 주고 받는데 상당히 어려움을 겪을 것이고 그것을 쉽게 해주는 것이 Serializer이다.


## Serializer vs ModelSerializer

Serializer Class를 정의할 때 기본적인 기능을 수행하는 method로는 Serializer와 ModelSerializer가 있다. (다른 기능을 수행하는 메소드들도 있다)  

2개의 차이를 간단하게 비교해보면 Serializer는 field를 1개씩 설정을 해줘야 한다는 점이다. 아래와 같이 models와 거의 비슷한 code를 작성하기 때문에 어느정도 반복적인 작업을 해줘야 한다.  

반면 ModelSerializer는 지정한 model Class의 필드들을 자동으로 가져온다. 아래와 같이 모든 필드를 사용할 때와 같이 "__all__"를 사용할 수도 있고 원하는 필드만 설정해주는 것도 가능하다.  

<br>

```python
# in models.py
class Snippet(models.Model):
    created = models.DateTimeField(auto_now_add=True)
    title = models.CharField(max_length=100, blank=True, default='')
    code = models.TextField()
    linenos = models.BooleanField(default=False)
    language = models.CharField(choices=LANGUAGE_CHOICES, default='python', max_length=100)
    style = models.CharField(choices=STYLE_CHOICES, default='friendly', max_length=100)

    owner = models.ForeignKey('auth.User', related_name='snippets', on_delete=models.CASCADE, null=True)
    highlighted = models.TextField(null=True)

    class Meta:
        ordering = ['created']
```

<br>

```python
# in serializers.py (Serializer 메소드 사용)
from rest_framework import serializers
from snippets.models import Snippet, LANGUAGE_CHOICES, STYLE_CHOICES
class SnippetSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    title = serializers.CharField(required=False, allow_blank=True, max_length=100)
    code = serializers.CharField(style={'base_template': 'textarea.html'})
    linenos = serializers.BooleanField(required=False)
    language = serializers.ChoiceField(choices=LANGUAGE_CHOICES, default='python')
    style = serializers.ChoiceField(choices=STYLE_CHOICES, default='friendly')
```

<br>

```python
# in serializers.py (ModelSerializer 메소드 사용)
from rest_framework import serializers
from snippets.models import Snippet, LANGUAGE_CHOICES, STYLE_CHOICES
class SnippetSerializer(serializers.ModelSerializer):
    class Meta:
        model = Snippet
        # fields = '__all__'
        fields = "__all__"
```