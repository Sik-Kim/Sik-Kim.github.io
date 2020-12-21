---
title: "Django ⎜ [DRF_TUTORIAL] Requests and Response"
date: 2020-12-21 20:25:00 -0400
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

# Tutorial 2 : Requests and Response

## Request object
request.data는 POST, PUT과 같이 body에 담아 보내지는 data이다.

```python
request.POST  # Only handles form data.  Only works for 'POST' method.
request.data  # Handles arbitrary data.  Works for 'POST', 'PUT' and 'PATCH' methods.
```

<br>


## Response object
response data는 요청에 대한 응땁하는 값으로 POST, PUT, GET 등에 사용할 수 있다.

```python
return Response(data)
```

<br>

## status codes
DRF에서만 제공하는 status codes가 있다.  
이 양식을 정확하게 지키지 않으면 에러가 발생되게 된다. (VSC의 자동완성이 빛을 발하는 순간...)  

ex)   
HTTP_400_BAD_REQUEST  
HTTP_200_OK

<br>

## views.py 작성 
**(request object, response object, status code 작성)**

DRF를 통해 request object, response object를 작성한 간단한 views.py 이다.  
데코레이터 @api_view는 기존적인 View 기능을 제공한다.  
다음번에 데코레이터 말고 더 많이 사용되는 class를 이용한 APIView라는 것을 사용할 것이다.  
주의 사항으론 POST나 PUT과 같이 변경사항이 있을 때는 반드시 serializer를 validation 해줘야 한다.  
아래 PUT 부분의 코드처럼 serializer.is_valid() 이 부분이 없으면 error가 발생된다.  

```python
@api_view(['GET', 'PUT', 'DELETE'])
def snippet_detail(request, pk):
    try:
        snippet = Snippet.objects.get(pk=pk)
    except Snippet.DoesNotExist:
        return Response(status=status.HTTP_404_NOT_FOUND)

    if request.method == 'GET':
        serializer = SnippetSerializer(snippet)
        return Response(serializer.data)

    elif request.method == 'PUT':
        serializer = SnippetSerializer(snippet, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == 'DELETE':
        snippet.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```