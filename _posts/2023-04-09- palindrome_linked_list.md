---
title: "Palindrome Linked List (leetcode 234)"
date: 2023-04-09 19:00:00 -0400
summary:
toc: ture
toc_sticky: true
comments: true
tag:
    - javascript
    - algorithm
category:
    - algorithm
---

## 문제

[문제 링크](https://leetcode.com/problems/palindrome-linked-list/)

![image](https://user-images.githubusercontent.com/63498876/230778598-f8263ec8-171c-418c-84d4-190438323bae.png)

<br>

## solution1

153ms (76.47%) <br>

```javascript
var isPalindrome = function (head) {
    let slow = head;
    let fast = head;
    let prev;
    let temp;
    while (fast && fast.next) {
        // Floyd's
        slow = slow.next; //[1,2,2,1] -> [2,2,1] -> [2,1]
        fast = fast.next.next; // [1,2,2,1] -> [2,1] -> null
    }
    prev = slow; //[2,1]
    slow = slow.next; //[2,1] -> [1]
    prev.next = null; // [2,1] -> [2] 역방향 순환 중단 무한루프 방지
    while (slow) {
        // reveral
        temp = slow.next; // null
        slow.next = prev; // [1] -> [1,2]
        prev = slow; // [2] -> [1,2]
        slow = temp; // [1,2] -> null
    }
    fast = head; // [1,2,2]
    slow = prev; // [1,2] (reveral된 slow 값)
    while (slow) {
        // comparison
        if (fast.val !== slow.val) return false;
        else {
            fast = fast.next; // [1,2,2] -> [2,2] -> [2]
            slow = slow.next; // [1,2] -> [2] -> null
        }
    }
    return true;
};
```

singly-linked list(단일 연결 리스트)에 관련된 문제이다. <br>
파이썬이나 자바스크립트에서 배열만 사용해봤지 리스트는 접해보지 않았던 자료구조라 풀지 못했다. <br>

**리스트가 사용되는 경우** <br>

1. 빠른 삽입/삭제가 필요한 경우: 리스트는 포인터를 이용해 다른 노드를 참조하는 방식으로 삽입/삭제가 빠름
2. 제한된 메모리 공간: 리스트는 필요할 때마다 노드를 동적으로 할당해서 제한된 메모리에 유용
3. 순차적 접근 필요없는 경우: 리스트는 배열과 달리 메모리상 순차적이지 않음
4. 재귀적 알고리즘

상황에 따라 리스트와 배열 중에 유용한 것을 사용하면 된다. <br>

그런데 사실상 자바스크립트에서 불리는 배열은 일반적인 배열과는 다르다. <br>
노드가 메모리에 순차적으로 저장되어 있지 않다. <br>
즉 자바스크립트에는 리스트와 배열은 사실상 없다고 볼 수 있고 배열을 흉내내는 객체가 있다. <br>

**문제풀이**
문제는 3가지 과정으로 풀이 된다.

1. Floyd's 알고리즘
   : slow, fast 2가지 pointer로 전체 리스트를 반으로 쪼갠다.
2. reversal
   : 이 문제의 핵심이다. slow 리스트를 리스트의 메서드만을 이용해서 뒤집어야 한다.
3. comparison
   : 뒤집은 slow와 fast를 노드 하나씩 비교한다.

slow를 뒤집기 위해서 prev, temp 변수가 동원되고 이 과정이 이해하는데 좀 어려웠었다.

<br>
