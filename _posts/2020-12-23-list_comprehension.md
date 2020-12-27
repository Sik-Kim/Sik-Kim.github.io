---
title: "Python ⎜ List 자신이 for문 내 선언될 경우의 List Complihension 작성 방법"
date: 2020-12-23 20:25:00 -0400
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


### List complihension  

**#1과 같이 생성하고자하는 list 자신이 for문 내 선언 될 경우 List Complihension은 어떻게 만들 수 있을까.**  


```python
branches = Branch.objects.all()

#1 (정상 작동)
brands = []
for branch in branches:
    if branch.brand not in brands:  # --- point
        brands.append(branch.brand)
```

<br>

**문제점**  

아래 #2-1과 같은 코드는 가장 익숙한 list complihension의 형태이다.  
하지만 이렇게 작성하면 brands 라는 list가 선언되지 않았기 때문에 에러가 발생된다.  
#2-2와 같이 빈 list를 미리 선언해주면 에러는 발생하지 않으나 brand 값들이 중복으로 들어가 원하는 결과를 얻지 못하게 된다. 
```python
#2-1 (Error 발생)
brands = [branch.brand for branch in branches if branch.brand not in brands]

#2-2 (에러는 없으나 brand 값이 중복으로 들어감)
brands = []
brands = [branch.brand for branch in branches if branch.brand not in brands]
```
<br>

**해결책**  

해결 방법은 간단하다.  
#3과 같이 append를 사용해주는 것이다.  

```python
#3 (정상 작동)
brands = []
[brands.append(branch.brand) for branch in branches if branch.brand not in brands]
```

<br>

### Set complihension  

마지막으로 #4와 같이 list 대신 set을 사용해서 complihension을 만들어 줄 수도 있다.  


```python
#4 (정상 작동, 다만 list와 다른 자료형으로 기능에 제약이 있을 수 있음)
brands = set()
[brands.add(branch.brand) for branch in branches]
```

하지만 set은 list와는 특성이 다른 자료형으로 list에 비해 제약이 많을 수 있다.  
가령 set은 index 개념이 없기 때문에 index가 필요한 메소드 적용시 아래와 같은 에러가 발생된다.  
> 'set' object is not subscriptable
