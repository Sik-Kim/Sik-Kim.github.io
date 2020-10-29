---
title: "헷갈리는 dictionary 문법 정리"
date: 2020-10-27 11:13:00 -0400
summary: 파이썬 dictionary 문법 정리
---

python의 여러가지 자료형 중 난 특히 dictionary가 쓸때마다 많이 헷갈렸던것 같다.  

더이상 여기저기 찾고 싶지 않아 정리한다..

### 빈 dictionary 생성

x = {}  
x = dict()  

<br>

### key, value를 이용하여 dictiionary 생성

x = {'a':10, 'b':20}  
x = dict(a=10, b=20)  
x = dict({'a':10, 'b':20})   &nbsp; # dict - dictionary  
x = dict([('a', 10), ('b', 20)])   &nbsp; # dict - list - tuple(key, value)  
x = dict(zip(['a', 'b'], [1, 2]))   &nbsp; # dict - zip - list(key), list(value)  


<br>

### dictionary 불러오기

A = x.keys()   &nbsp; # 모든 key값 list로 불러오기  
B = x.values()   &nbsp; # 모든 value값 list로 불러오기  


<br>


### dictionary 추가(수정)
numbers = {'a':10, 'b':20, 'c':30}

numbers['d'] = 40  &nbsp; # 새로운 key, value 할당  
- numbers['a'] = 15 &nbsp; # 기존 key값의 value 수정  

<br>

### dictionary 삭제
del numbers['c']  &nbsp; # 해당되는 key, value 삭제  
numbers.pop('c')  &nbsp; # 해당되는 key, value 삭제  
numbers.clear()   &nbsp; # numbers 모든 key, value 삭제(빈 dic인 {}은 남음)

<br>

### dictionary 끼리 합치기
x1 = {'a':10, 'b':20}  
x2 = {'a':10, 'b':20}  

x1.update(x2)   &nbsp; # x1에 x2의 key, value가 추가됨, x2 값은 그대로 남아있음  
x3 = dict(x1, **x2)  &nbsp; # x1, x2가 합쳐서 x3에 할당됨  

<br>

### dictionary key 있는지 확인
'a' in numbers  
True  

'b' not in numbers  
False  

<br>

### Dictionary key 개수 구하기  
len(numbers)  
len({'a'=10, 'b'=20, 'c'=30})


