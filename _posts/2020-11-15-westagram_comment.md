---
title: "Django ⎜ Instagram 댓글(comment) 기능 구현"
date: 2020-11-15 11:10:00 -0400
summary: 
toc : ture
toc_sticky: true
comments: true
tag : 
    - TIL
    - django
    - instagram
    - comment
category : 
    - django
layout: post
lastmod : 2020-11-15 12:00:00
sitemap :
changefreq : daily
priority : 1.0
---

# instagram 댓글 구현하기

<br>

## Preview
instagram 댓글을 구현하며 처음으로 내가 쓴 코드라는 생각이 들었다.  
회원가입, 로그인, 인증/인가 등 이전에는 다른 코드를 참조하며 억지로 코드를 만들고 그 코드를 이해하기 바빴다.  
**하지만 이번엔 무작정 코드를 쓰기 시작하기 전에 나도 생각이란 걸 해봤다.**

![think zzal](https://i.ibb.co/B6LZj0w/think.jpg)

<br>

모델링을 생각해보고 참조값, 예외처리 등 코드에 필요한 내용을 노트에 쭉 써보니 뭔가 정리가 되는 느낌이었다.  
아 코드는 이렇게 짜는 거구나 하는 느낌이 왔다. ~~차각은 자유니까..~~

<br>

## 댓글 구현을 위해 고려한 점
1. ERD diagram(model, type)
2. 로그인 데코레이터 구현
3. comments table database에 저장될 정보
4. request 내용
5. 예외 처리에는 무엇이 있는지.
6. 참조값 적용 방안

<br>

## models.py

model은 비교적 간단하게 만들었다.  
ForeignKey로 참조하는 Class명을 넣을 때 보통 ''를 써줬는데 import를 해주면 '' 안써줘도 참조가 가능하다.

```python
from django.db import models
from user.models import User

class Comment(models.Model):
    comment = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    image_url = models.ForeignKey(Posting, on_delete=models.CASCADE)
    user = models.ForeignKey(User, on_delete=models.CASCADE)

    class Meta:
        db_table = "comments"

    def __str__(self):
        return self.comment

```

<br>

## views.py

views.py를 작성할 때는 코드의 흐름을 정확하게 이해하기 위해서 코드 한 줄마다 print문을 찍어보았다.  
print문으로 코드를 조목조목 상세하게 살펴보는 게 코드를 이해하고 에러를 파악하는데 엄청나게 도움이 되었다.
(코드컨벤션 무시하고 싶은 건 아닌데... 이번 instagram만 print문 좀 쓰려고요...)

참조키를 어떻게 가져와서 적용할지가 매번 어려웠었다.
우선 user 정보는 미리 만든 로그인 데코레이터에 이미 request.user로 속성을 만든 게 있어 쉽게 해결했다.
그리고 image_url 참조 값은 이미 가지고 있는 user 정보와 request로 요청할 image_url를 이용하여 데이터값을 찾았다.

```python

class CommentView(View):
    @login_decorator
    def post(self, request):
        data = json.loads(request.body)
        print("---------------CommentView-------------------")
        print("-----request-----", request)
        print("-----request.body-----", request.body)
        print("-----data-----", data)

        user = request.user
        print("-----user------", user)

        try:
            user_of_posting = User.objects.filter(user_name=data["user_of_posting"])
            print("-----user_of_posting(QuerySet)-----", user_of_posting)

            image_url = Posting.objects.filter(
                image_url=data["image_url"], user=user_of_posting.first()
            )
            print("-----image_url(QuerySet)-----", image_url)

            if not user_of_posting.exists():
                return JsonResponse({"message": "user of posting does not exist!!"})

            if not image_url.exists():
                return JsonResponse({"message": "image url does not exist!!"})

            Comment.objects.create(
                comment=data["comment"], image_url=image_url.first(), user=user
            )

            return JsonResponse({"message": "comment upload success"}, status=200)
        except KeyError:
            return JsonResponse({"message": "KeyError!!"}, status=400)
        except Exception as ex:
            return JsonResponse({"message": f"{ex}"}, status=400)
```

<br>

## login_decorator
데코레이터는 따로 포스팅을 할 생각으로 별도로 언급하지 않고 코드만 넣어두겠다.
데코레이터도 코드 분석을 위해 print문을 잔득 넣어뒀다..
(데코레이터와 지지고 볶은 사연도 어휴....)

```python
import jwt
import json

from django.http import JsonResponse
from django.core.exceptions import ObjectDoesNotExist
from django.db.models import Q

from .my_settings import SECRET, ALGORITHM
from user.models import User


def login_decorator(original_function):
    def wrapper(self, request, *args, **kwargs):
        try:
            access_token = request.headers.get("Authorization", None)

            payload = jwt.decode(
                access_token, SECRET["secret"], algorithms=ALGORITHM["algorithm"],
            )  
            print("-----------------login_decorator-----------------")
            print("-----payload-----", payload)

            user = User.objects.filter(
                Q(user_name=payload["account"])
                | Q(mobile_number=payload["account"])
                | Q(email=payload["account"])
            )


            request.user = user.first()
            print("-----request.user-----", request.user)
            print("-----request.user.id-----", request.user.id)
            print("-----request.user.mobile_number-----", request.user.mobile_number)
            print("-----request.user.email-----", request.user.email)


        except jwt.DecodeError:
            return JsonResponse({"message": "invalid token!!"}, status=400)

        except User.DoesNotExist:
            return JsonResponse({"message": "invalid user"}, status=400)

        return original_function(self, request, *args, **kwargs)

    return wrapper

```

<br>

## request 내용
httpie 프로그램을 사용해서 request를 서버로 전송했다.
우선 login decorator를 이용한 로그인 인증을 위해 access token인 Authorization를 포함했다. 그리고 posting의 user와 image_url과 comment 내용을 담았다.

```
http -v POST 127.0.0.1:8000/posting/comment Authorization:'eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhY2NvdW50IjoiamVubnkifQ.vcObfkUOTTovDXl-B-f7K8uS6DeaF7d1bbExDyBPx1s' user_of_posting='salvame21' 
image_url='https://i.ibb.co/6XZZb3v/zzal-start.jpg' 
comment='Good night!!!!'
```

<br>

## Terminal 화면
Terminal 화면에 보이는 success 라는 문구가 주는 희열이란....(끄덕 끄덕)

![terminal](https://i.ibb.co/gy3HpFv/message.png)

<br>

![comment200ok](https://i.ibb.co/Wzgwr6Y/comment200ok.png)




<br>

## Database Table(Mysql)
여러번 request 하다보니 중복된 data 많은점 유의..

![comment_table](https://i.ibb.co/S3xbd35/comment-db.png)