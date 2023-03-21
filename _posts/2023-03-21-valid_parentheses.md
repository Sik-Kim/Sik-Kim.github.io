---
title: "Valid Parentheses (leetcode 20)"
date: 2023-03-21 19:00:00 -0400
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

[문제 링크](https://leetcode.com/problems/valid-parentheses/)

![image](https://user-images.githubusercontent.com/63498876/226553732-736de77b-e7b3-490e-a8d6-af22543d253f.png)

<br>

## solution1

-   속도: 57ms (92.74%)
-   메모리: 42.5mb (57.74%)

```javascript
var isValid = function (s) {
    const stack = [];
    const obj = {
        ")": "(",
        "]": "[",
        "}": "{",
    };
    for (let i = 0; i < s.length; i++) {
        const value = s[i];
        if (["(", "{", "["].includes(value)) {
            stack.push(value);
        } else {
            if (stack.pop() !== obj[value]) return false;
        }
    }
    if (stack.length) return false;
    return true;
};
```

스택의 원리를 이용해서 짝을 만날때마다 pop을 시켰다. <br>
속도도 빠르게 나오고 코드도 쉽게 작성됐다. <br>
구 for문 말고 for of를 사용하면 속도는 거의 비슷한데 메모리가 44.3mb(10.43%)로 아주 느린편이다. <br>
그러나 절대값 자체는 큰 차이가 없기에 가독성 좋은 for of를 사용하는것도 나쁘지 않은 것 같다.

<br>

## solution2

-   속도: 81ms (18.84%)
-   메모리: 47.3mb (7.64%)

```javascript
var isValid = function (s) {
    let i = 0;
    let replacedStr = s;
    while (i < s.length) {
        replacedStr = replacedStr
            .replaceAll("()", "")
            .replaceAll("[]", "")
            .replaceAll("{}", "");
        i += 2;
    }
    return replacedStr === "";
};

isValid("([])"); // true
isValid("()[]{}"); // true
isValid("(]"); // false
isValid("["); // false
```

replaceAll을 사용해서 문자를 없애나가는 방식이다. <br>
반복해서 없애고 남는 값이 없다면 true로 판단했다. <br>
replaceAll을 3번씩 사용해서인지 속도가 엄청 느리게 나온다. <br>
replace가 여러번 연달아 사용하는건 속도에 영향을 미칠 수 있단걸 인지하자!

<br>
