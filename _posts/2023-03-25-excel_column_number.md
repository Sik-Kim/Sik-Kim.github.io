---
title: "Excel Sheet Column Number (leetcode 171)"
date: 2023-03-25 22:00:00 -0400
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

[문제 링크](https://leetcode.com/problems/excel-sheet-column-number/description/)

![image](https://user-images.githubusercontent.com/63498876/227719333-22feebce-9e53-493a-b99c-c6ee9b13c1ed.png)

<br>

## solution1

-   속도: 81ms (32.87%)
-   메모리: 44.1mb (18.61%)

```javascript
var titleToNumber = function (columnTitle) {
    let j = 0;
    let sum = 0;
    for (let i = columnTitle.length - 1; i >= 0; i--) {
        sum += AlphabetToNumber(columnTitle[i]) * Math.pow(26, j);
        j += 1;
    }
    return sum;
};

function AlphabetToNumber(str) {
    return str.charCodeAt(0) - 64;
}
```

패턴을 발견하는게 어렵진 않아서 금방 풀었는데.. 속도나 메모리가 좋지 않다. <br/>
알고리즘은 눈에 보이는데로만 풀면 풀리긴 하는데 효율은 좀 떨어지는 것 같다.. <br/>
빠른 속도의 해답이 어떨지 궁금하다.

<br>

## solution2

-   속도: 65ms

```javascript
var titleToNumber = function (title) {
    let res = 0;
    for (let i = 0; i < title.length; ++i)
        res = res * 26 + title.charCodeAt(i) - 64;
    return res;
};
```

와. 이렇게 풀었구나 <br/>
나는 위에서 뒤에서부터 index를 접근하며 숫자를 더했는데 이 방법은 앞에서부터 접근한다. <br/>
문자열이 하나 늘 때 마다 모든 문자열의 값에 26을 곱한다.. 위에서 처럼 한자리씩 26을 곱해서 더할 필요가 없다! (이게 알고리즘적인 사고이다.) <br/>
가능하면 index는 0부터 접근하는 패턴을 찾자.

<br>
