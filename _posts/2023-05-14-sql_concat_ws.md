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
concat_ws는 또한 null인 값은 제외하고 값을 합쳐주기에 별도 예외처리가 필요없다. <br>

<br>

## 쿼리단에서의 전처리 장점과 유의할점

쿼리를 사용하는 환경이라면 쿼리를 불러올 때 처리 가능한 연산들은 처리해주면 좋다. <br>
문자열, 숫자, Date 등 웬만한 연산들은 쿼리단에서 끝나는 것들이 많다. <br>

**쿼리단 전처리 장점**

-   상위 수준의 코드가 간결해지고 가독성이 좋아진다.
-   코드가 간결해지니 에러가 줄어든다.
-   db에서 처리하는게 속도면에서 빠르다.

**유의할 점**

-   재사용성이 좋지 않을 수 있다. ORM을 사용하는 환경에서는 맞지 않을 수 있다.
-   너무 복잡한 쿼리는 가독성도 좋지 않다.
