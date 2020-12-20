---
title: "Django ⎜ [DRF_TUTORIAL] Serialization & Deserialization"
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

# Tutorial 1 : Serialization - (2)

## Serialization & Deserialization 과정
DRF의 메소드를 사용해서 serializer.data를 json으로 변환시키는 Serialization과 다시 python nativave datatype으로 변환시키는 Deserialization 과정을 수행해보려한다.  
정확히 어떤식으로 변환되는건지 한줄씩 이해하기 위해 print문이 많이 사용했다.

<br>

### Django Code

```python
# models.py
from django.db import models
from pygments.lexers import get_all_lexers
from pygments.styles import get_all_styles

LEXERS = [item for item in get_all_lexers() if item[1]]
LANGUAGE_CHOICES = sorted([(item[1][0], item[0]) for item in LEXERS])
STYLE_CHOICES = sorted([(item, item) for item in get_all_styles()])
```

<br>

```python
# models.py

class Snippet(models.Model):
    created = models.DateTimeField(auto_now_add=True)
    title = models.CharField(max_length=100, blank=True, default='')
    code = models.TextField()
    linenos = models.BooleanField(default=False)
    language = models.CharField(choices=LANGUAGE_CHOICES, default='python', max_length=100)
    style = models.CharField(choices=STYLE_CHOICES, default='friendly', max_length=100)

    class Meta:
        ordering = ['created']
```

<br>

```python
# serializers.py
from rest_framework import serializers
from snippets.models import Snippet, LANGUAGE_CHOICES, STYLE_CHOICES

class SnippetSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    title = serializers.CharField(required=False, allow_blank=True, max_length=100)
    code = serializers.CharField(style={'base_template': 'textarea.html'})
    linenos = serializers.BooleanField(required=False)
    # language = serializers.ChoiceField(choices=LANGUAGE_CHOICES, default='python')
    # style = serializers.ChoiceField(choices=STYLE_CHOICES, default='friendly')
```

<br>

```python
# views.py
from django.http import request, JsonResponse
import io

from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from rest_framework.renderers import JSONRenderer
from rest_framework.parsers import JSONParser

from .serializers import SnippetSerializer
from .models import Snippet

class TestView(APIView):
    def get(self, request):
        serializer = SnippetSerializer(Snippet.objects.get(id=2))

        print("\n")
        serializer.data
        print("-----serializer-----\n",serializer)
        print("-----serializer.data-----\n",serializer.data)
        print("-----type(serializer.data)-----\n",type(serializer.data))
        print("-----type(serializer)-----\n",type(serializer))
        print("\n")
        
        json = JSONRenderer().render(serializer.data) # python native datatype -> json(Serialization)
        print("-----json-----\n",json)
        print("-----type(json)-----\n",type(json))
        print("\n")

        stream = io.BytesIO(json) # json -> python native datatype(Deserialization)
        data = JSONParser().parse(stream)
        print("-----data-----\n",data)
        print("-----type(data)-----\n",type(data))
        print("\n")

        serializer = SnippetSerializer(data=data)
        print("-----serializer-----\n",serializer)
        print("-----type(serializer)-----\n",type(serializer))
        print("-----is_valid() 안하고 serializer.data하면 error 발생됨")
        print("\n")
        
        
        serializer.is_valid()
        print("-----serializer.is_valid()-----\n",serializer.is_valid())
        print("-----serializer.data-----\n",serializer.data)
        print("-----type(serializer.data)-----\n",type(serializer.data))
        print("\n")

        serializer.validated_data
        print("-----serializer.validated_data-----\n",serializer.validated_data)
        print("-----type(serializer.validated_data)-----\n",type(serializer.validated_data))
        # serializer.save()  # 앞에 print(serializer.data) 이거 있음 에러
        print("\n")

        serializer = SnippetSerializer(Snippet.objects.all(), many=True)
        print("-----serializer.data-----\n",serializer.data)
        print("-----type(serializer.data)-----\n",type(serializer.data))
        print("\n")

        return Response({'data': serializer.data}, status=status.HTTP_200_OK)
```

<br>

### server 화면
다른 부분은 말고 -----json-----, -----data------ 의 아래 부분만 참고하면 된다.  
Serialization과 Deserialization 하는 과정이다.

![server 화면](https://i.ibb.co/W6xnvxq/image.png)

<br>

### response data 화면
![response data](https://i.ibb.co/6RkrfVB/image.png)