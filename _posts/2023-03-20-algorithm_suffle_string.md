---
title: "Shuffle String (leetcode 1528)"
date: 2023-03-21 01:26:00 -0400
summary: 
toc : ture
toc_sticky: true
comments: true
tag : 
    - javascript
    - algorithm
category : 
    - algorithm
---

![image](https://user-images.githubusercontent.com/63498876/226405914-995c1dd2-410f-4858-ab8a-042e424e249f.png)

<br>

### 내가 푼 방법 (60ms)
1. 객체에 key는 숫자(0부터), value는 string 값을 매칭 시킨다.
2. 0부터 값 가져와서 스트링 조합

```javascript
var restoreString = function(s, indices) {
    const len = s.length
    let i = 0
    let j = 0
    let obj = {}

    // obj 생성
    while (i<len) {
        obj[indices[i]] = s[i]
        i += 1
    }

    // shuffle string
    let shuffleString = ''
    while (j<len) {
        shuffleString += obj[j]
        j += 1
    }
    return shuffleString
};

const s = 'codeleet'
const indices = [4,5,6,7,0,2,1,3]

restoreString(s, indices) // 'leetcode'
```

속도는 제출할 때마다 조금씩 차이가 있었지만 최대 93.5%까지 나왔다. <br>
즉, 객체를 검색 용도로 사용하는건 속도측면에서도 좋고 코드 쓰기도 편리한 것 같다.

<br>

## 다른 해석 (59ms)
```javascript
var restoreString = function(s, indices){
    let output = ''
    for (let i=0; i<s.length; i++){
    output += s[indices.indexOf(i)];
    }
    return output
}

const s = 'codeleet'
const indices = [4,5,6,7,0,2,1,3]

restoreString(s, indices) // 'leetcode'
```

indexOf를 평소 잘 안써보다보니 생각을 못했다. <br>
속도도 빠른데 코드도 간단해서 가독성도 좋다. <br>
배열의 요소가 뒤섞여 있는데 내가 원하는 어떤 순서대로 가져오고 싶을 때 indexOf를 사용하자!

