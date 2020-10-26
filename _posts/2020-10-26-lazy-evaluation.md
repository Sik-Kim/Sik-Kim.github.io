---
title: "Lazy evaluation이란? list comprehension과 비교"
date: 2020-10-26 22:37:00 -0400
summary: lazy evaluation 함수, list comprehension과 코드 비교
---

## lazy evaluation

<br>

lazy evaluation 함수는 말 그대로 실행을 지연시키는 것입니다.  

generator 함수가 호출 될 떄까지 그 값의 계산을 뒤로 미루는 동작 방식입니다.
불필요한 실행을 막으며 메모리 낭비(리소스 낭비)를 줄일 수 있고 이를 통해 코드최적화를 위해 사용되는 함수입니다. 

 예를 들어 제가 호텔 주인이고 겨울에 방이 10개 있습니다. 오늘 예약을 받은 방은 한개도 없을 때 올지도 모르는 손님을 위해 10개의 방에 난방을 틀어놓는 것이 일반 함수 호출이라고 할 수 있습니다. lazy evaluation은 난방 트는 것을 지연시켜서 손님이 올때 난방을 틀어주는 것으로 볼 수 있습니다. 그럼 불필요하게 난방 트는 것을 막을 수 있습니다.

<br>
<br>

### list comprehension vs lazy evaluation

<br>

그럼 코드 예제를 통해 lazy evaluation이 어떻게 쓰이는지 알아보겠습니다.
 - list comprehension : [ ] 사용
 - lazy evaluation : ( ) 사용

<br>

**list comprehension 코드**

```python
import time

L = [ 1,2,3]

def print_iter(iter):
    for element in iter:
        print(element ** 2)

def lazy_return(num):
    print("sleep 1s")
    time.sleep(1)
    return num

print("comprehension_list=")
comprehension_list = [ lazy_return(i) for i in L ] 
print_iter(comprehension_list)

# print("generator_exp=")
# generator_exp = ( lazy_return(i) for i in L )
# print_iter(generator_exp)  
```

<br>

**결과값**  

```
comprehension_list=
sleep 1s
sleep 1s
sleep 1s
1
4
9
```

<br>
<br>


**lazy evaluation 코드**

```python
import time

L = [ 1,2,3]

def print_iter(iter):
    for element in iter:
        print(element ** 2)

def lazy_return(num):
    print("sleep 1s")
    time.sleep(1)
    return num

# print("comprehension_list=")
# comprehension_list = [ lazy_return(i) for i in L ]
# print_iter(comprehension_list)

print("generator_exp=")
generator_exp = ( lazy_return(i) for i in L )
print_iter(generator_exp)  
```

<br>

**결과값**  

```
generator_exp=
sleep 1s
1
sleep 1s
4
sleep 1s
9
```

generator_exp = (   ) 가 generator 함수로 print_iter(generator_exp)에서 generator 함수가 실행되면서 지연되었던 lazy evaluation 함수가 실행이 되는 것입니다.

<br>
<br>

### 결론
코드 최적화를 위해서 무조건적인 lazy evaluation 함수를 남발은 좋지 않습니다.  
실행되지 않는 함수 요소가 많아 불필요한 함수가 반복적으로 실행될 때 lazy evaluation을 사용하는 것이 좋습니다.




