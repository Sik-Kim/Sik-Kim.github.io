---
title: "Climbing Stairs (leetcode 70)"
date: 2023-03-26 19:00:00 -0400
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

[문제 링크](https://leetcode.com/problems/climbing-stairs/)

![image](https://user-images.githubusercontent.com/63498876/227769660-ccad5229-8eca-4629-983b-e3a7affe2b90.png)

<br>

## solution1

51ms (92.82%) <br>

```javascript
var climbStairs = function (n) {
    let one = 0;
    let two = 1;
    let sum;
    for (let i = 0; i < n; i++) {
        sum = one + two;
        one = two;
        two = sum;
    }
    return sum;
};
```

n=1부터 결과값을 하나씩 쓰다보면 1,2,3,5,8,13.... 등 피보나치 수열 패턴이 나온다. <br>
이 패턴을 알고나면 코드는 간단하게 작성이 가능하다. <br>
역시 심플한게 속도도 빠르다.

<br>

## solution2

54ms (86.39%) <br>

```javascript
var climbStairs = function (n) {
    let result = 0;
    for (i = Math.round(n / 2); i <= n; i++) {
        result += factorial(i, n - i) / factorial(n - i, n - i);
    }
    return result;
};

function factorial(num, n) {
    if (num === 0 || n < 1) {
        return 1;
    } else {
        return num * factorial(num - 1, n - 1);
    }
}
```

n에 숫자 하나를 대입하고 계단수별로 경우의 수를 계산하는 패턴을 찾았다. <br>
팩토리얼이 사용됐고 패턴대로 하면 결과 값은 잘 출력된다. <br>
하지만 복잡해서 패턴을 작성하는데 오래걸리고 가독성도 상당히 나쁘다.

<br>

![image](https://user-images.githubusercontent.com/63498876/227769807-88ad6505-a97c-4091-9d72-2a4a71359b22.png)
