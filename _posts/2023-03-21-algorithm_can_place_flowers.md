---
title: "Can Place Flowers (leetcode 605)"
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

[문제 링크](https://leetcode.com/problems/can-place-flowers)

![image](https://user-images.githubusercontent.com/63498876/226490893-5ee79334-8471-4d36-ab76-c903f6c4634b.png)

<br>

## solution1

-   속도: 56ms

```javascript
var canPlaceFlowers = function (flowerbed, n) {
    let flowers = 0;
    for (let i = 0; i < flowerbed.length; i++) {
        if (flowerbed[i] === 1) {
            continue;
        }

        if (flowerbed[i - 1] !== 1 && flowerbed[i + 1] !== 1) {
            flowers++;
            i++;
        }
    }

    return flowers >= n;
};

canPlaceFlowers([1, 0, 0, 0, 1], 1); // true
canPlaceFlowers([1, 0, 0, 0, 1], 2); // false
```

현실 세계와 동일한 구조로 알고리즘을 짰다고 볼 수 있다. <br>
화분 하나씩 확인해보는 것이다. (1. 비어 있는지 2. 양옆에 심어진 화분이 없는지) <br>

<br>

## solution2 (my answer)

-   속도: 62ms (85.3%)
-   메모리: 45.4mb (10.76%)

```javascript
var canPlaceFlowers = function (flowerbed, n) {
    let str = "0" + flowerbed.join("") + "0";
    let a1 = str.split("1");
    let a2 = a1.filter((e) => e && e !== "00" && e !== "0");
    let a3 = a2.map((e) => e.replace("000", "A"));
    let a4 = a3.map((e) => e.replaceAll("00", "A"));
    let str2 = a4.join("");
    const count = (str2.match(/A/g) || []).length;
    return count >= n;
};

canPlaceFlowers([1, 0, 0, 0, 1], 1); // true
canPlaceFlowers([1, 0, 0, 0, 1], 2); // false
```

위에 답변과 달리 현실 세계와 생각 로직과 다른 방법을 찾다가 멀리 돌아온 것 같다. <br>
속도가 느리지는 않지만 가독성이 좋지않고 노가다스러운 해결방법이다. <br>

변수를 많이 써서 메모리도 많이 차지 한다. <br>
변수가 쓸데없이 남발되는건 아닌지 의심해볼 필요가 있다! <br>

<br>
