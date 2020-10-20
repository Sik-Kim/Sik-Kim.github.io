---
title: "[HTML/CSS] Table 만들기"
date: 2020-10-20 12:52:00 -0400
summary: table 만드는 방법
---



### HTML Table 만들기

html의 tag를 이용하여 table을 만들 수 있습니다.

> tr(table row) : row 생성

> td(table data) : table 내 data 생성, column 갯수 결정

> th : td와 역할은 같으나 자동으로 bold, 가운데 정렬이 적용된다.

tr, td, th를 사용해서 간단한 2x3 table을 만들어 보겠습니다.


```html
  <table>
    <tr>
      <th>Row 1, column 1</th>
      <th>Row 1, column 2</th>
      <th>Row 1, column 3</th>
    </tr>
    <tr>
      <td>Row 2, column 1</td>
      <td>Row 2, column 2</td>
      <td>Row 2, column 3</td>
    </tr>
  </table>
```

<br>

table 결과

![table](/img/2020-10-20/table1.png)

html로만 table을 작성하면 위와 같은 결과가 나오는데 뭔가 밋밋하다. 따라서 css로 가로,세로선 등 table에 대한 속성을 입혀줘야 합니다.



<br>

<br>

### table 병합하기

td, th tag에 rowspan, colspan 속성을 사용해서 행과 열의 병합을 할 수 있습니다.

> rowspan="병합할 행 개수" : 행 병합

> colspan="병합할 열 개수" : 열 병합

아래는 열 병합 적용 html 코드입니다.

```html
 <table class="my_table">
      <tr>
        <th></th>
        <th>1pm</th>
        <th>2pm</th>
        <th>3pm</th>
      </tr>
      <tr>
        <th>Gym</th>
        <td>Dodge ball</td>
        <td>Kick boxing</td>
        <td>Sack racing</td>
      </tr>
      <tr>
        <th>Exercise Room</th>
        <td>Spinning</td>
        <td colspan="2" class="my_td">Yoga marathon</td>
        </tr>
      <tr>
        <th>Pool</th>
        <td colspan="3" class="my_td">Water polo</td>
      </tr>
      </table>
```

<br>

table 결과

![table](/img/2020-10-20/table2.png)

<br>
<br>

### table에 css 적용하기

일반 text와 마찬가지로 tag(tr, td, th)나 class로 css selector를 설정하여 css를 적용할 수 있습니다.

```css
.my_table th, .my_table td {
  border: 1px solid black;
}

.my_td {
  background-color: grey;
}

th{
  font-weight: 400;
  text-align: left;
}
```

<br>

css 적용 결과

![table](/img/2020-10-20/table3.png)

