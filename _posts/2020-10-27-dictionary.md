---
title: "헷갈리는 dictionary 문법 정리"
date: 2020-10-27 11:13:00 -0400
summary: 파이썬 dictionary 문법 정리
---

## 파이썬 dictionary 문법 정리

<br>
<br>

### dictionary 생성

**빈 dictionary 생성**  
x = {}  
x = dict()  

**key, value를 이용하여 dictiionary 생성**  
x = {'a':10, 'b':20}  
x = dict(a=10, b=20)  
x = dict({'a':10, 'b':20})   &nasp # dict - dictionary  
x = dict([('a', 10), ('b', 20)])   &nasp # dict - list - tuple(key, value)  
x = dict(zip(['a', 'b'], [1, 2]))   &nasp # dict - zip - list(key), list(value)  


<br>

### dictionary 불러오기

A = x.keys()   &nasp # 모든 key값 list로 불러오기  
B = x.values()   &nasp # 모든 value값 list로 불러오기  


<br>


### dictionary 추가(수정)
numbers = {'a':10, 'b':20, 'c':30}

numbers['d'] = 40  &nasp # 새로운 key, value 할당  
- numbers['a'] = 15 &nasp # 기존 key값의 value 수정  

<br>

### dictionary 삭제
del numbers['c']  &nasp # 해당되는 key, value 삭제
numbers.pop('c')  &nasp # 해당되는 key, value 삭제
numbers.clear()   &nasp # numbers 모든 key, value 삭제(빈 dic인 {}은 남음)

<br>

### dictionary 끼리 합치기
x1 = {'a':10, 'b':20}  
x2 = {'a':10, 'b':20}  

x1.update(x2)   &nasp # x1에 x2의 key, value가 추가됨, x2 값은 그대로 남아있음  
x3 = dict(x1, **x2)  &nasp # x1, x2가 합쳐서 x3에 할당됨  

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


