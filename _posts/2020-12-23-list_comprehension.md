---
title: "Python ⎜ 변수 선언이 안되서 List Complihension이 불가 할 때 - Set Complihension 만들기"
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

### complihension 만들기  

<br>

**문제점**  
#1과 같은 for문을 List Complihension을 만들려고 했으나 if문에 아직 변수선언이 되지 않은 branchs 때문에 에러가 발생됐다.  
중복되지 않은 brand만 brands라는 list에 담으려는게 목적으로 list 대신 set을 사용해서 해결할 수 있었다.  


```python
branches = Branch.objects.all()

#1 (정상 작동)
brands = []
for branch in branches:
    if branch.brand not in brands:
        brands.append(branch.brand)

#2 (Error 발생)
brands = [branch.brand for branch in branches if branch.brand not in brands]
```

<br>

**해결책**


```python
#3 (정상 작동)
brands = set()
[brands.add(branch.brand) for branch in branches]
```