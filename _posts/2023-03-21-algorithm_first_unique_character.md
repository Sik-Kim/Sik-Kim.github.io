---
title: "First Unique Character (leetcode 387)"
date: 2023-03-21 09:00:00 -0400
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

[문제 링크](https://leetcode.com/problems/first-unique-character-in-a-string/)

![image](https://user-images.githubusercontent.com/63498876/226494569-e854510f-93e9-4a73-b8d1-646c8c3f5366.png)

<br>

## solution1

-   속도: 95ms (84.8%)
-   메모리: 44.4mb (95%)

```javascript
var firstUniqChar = function (s) {
    index = 0;
    while (index < s.length) {
        if (s.indexOf(s[index]) === s.lastIndexOf(s[index])) return index;
        index += 1;
    }
    return -1;
};

firstUniqChar("leetcode"); // 0
firstUniqChar("loveleetcode"); // 2
firstUniqChar("aabb"); // -1
```

이 방법이 가장 가독성 좋고 간단한 방법 같다. <br>
결국 여기서는 lastIndexOf 메서드를 알고 있느냐가 핵심이다. <br>

<br>

## solution2

-   속도: 565ms (5%)
-   메모리: 49.2mb (15.4%)

```javascript
var firstUniqChar = function (s) {
    index = 0;
    while (index < s.length) {
        let zz = s.substring(0, index) + s.substring(index + 1);
        if (!zz.includes(s[index])) return index;
        index += 1;
    }
    return -1;
};

firstUniqChar("leetcode"); // 0
firstUniqChar("loveleetcode"); // 2
firstUniqChar("aabb"); // -1
```

속도 느린걸로 정점을 찍는 방법이다. 그래서 또 의미가 있다.. <br>
문자열을 커스텀 하는 방식이 일반적이지 않고 굳이 이렇게까지 해야하나? 싶은 상황이다. <br>
이런 느낌이 든다면 대체로 잘못하고 있을 확률이 높다!

<br>
