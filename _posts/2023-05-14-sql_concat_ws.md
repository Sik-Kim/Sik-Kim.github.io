---
title: "[Postgresql] concat_ws 문자열 합치기 (코드 리팩토링)"
date: 2023-05-13 16:00:00 -0400
summary:
toc: ture
toc_sticky: true
comments: true
tag:
    - sql
    - postgresql
category:
    - SQL
---

## concat, concat_ws

concat, concat_ws을 활용하면 테이블의 각 칼럼의 값들을 합쳐서 한번에 조회 가능하다.

-   concat: 칼럼의 값들을 하나의 문자열로 합쳐준다.
-   concat_ws: 칼럼의 값들을 구분자를 포함하여 하나의 문자열로 합쳐준다. (ws = with separator)

아래는 concat_ws를 사용해서 실제로 코드도 간결해지고 코드수도 줄인 예시다.

<br>

#### 수정 전 쿼리

```javascript
const query = `
   select si, gu_parent, gu_child, dong from region;
`;
const res = await client.query(query);
const data = res.rows[0];
let address;
if (data.gu_child) {
    address = `${si} ${gu_parent} ${gu_child} ${dong}`; // 경기도 성남시 분당구 판교동
} else {
    address = `${si} ${dong}`; // 세종특별자치시 보람동
}
```

#### 수정 후 쿼리

```javascript
const query = `
   select concat_ws( ' ', si, gu_parent, nullif(gu_child, ''), dong) as address from region;
`;
const res = await client.query(query);
const data = res.rows[0];
const address = data.address; // 경기도 성남시 분당구 판교동, 세종특별자치시 보람동
```

concat_ws를 사용해서 si, gu_parent, gu_child, dong 4개 칼럼의 값들을 합쳤고 중간에 ' ' 띄어쓰기 구분자를 넣어줬다. <br>

또한 concat_ws는 좋은점이 null인 값은 제외하고 더해준다는 것이다. <br>
예를 들어 gu_parent, gu_child 칼럼 값이 null이라면 si, dong 2개 칼럼값만 합쳐준다.

<br>

## 결론

가능하면 쿼리로 작성할 수 있는 로직은 쿼리에서 해주면 좋다. <br>
concat_ws 외에도 숫자 또는 시간 계산, 불린값 등 리팩토링을 하다보면 생각보다 쿼리단에서 끝나는 것들이 많다. <br>

sql로 데이터를 연산하면 좋은점은 우선 코드가 간결해진다. <br>
코드가 간결해지면 에러도 줄고 재사용하기도 좋아진다. <br>
또한 대부분의 경우 db에서 처리하는게 속도도 빠르다.

그러니 쿼리가 좀 길어지더라도 최대한 sql을 활용하자. 끗
