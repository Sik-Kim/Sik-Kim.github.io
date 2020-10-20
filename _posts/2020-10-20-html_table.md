---
title: "[HTML/CSS] Table 만들기"
date: 2020-10-20 12:52:00 -0400
summary: table 만드는 방법
---



### HTML Table 만들기

html의 tag를 이용하여 table을 만들 수 있습니다.

tr(table row) : row 생성

td(table data) : table 내 data 생성, column 갯수 결정

th : td와 역할은 같으나 자동으로 bold, 가운데 정렬이 적용된다.

2 x 3 table

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

<br>

### table 병합하기

rowspan="병합할 행 개수" : 행 병합

colspan="병합할 열 개수" : 열 병합

```html
 <table class="my_table">
      <tr>
        <th></th>
        <th>1pm</th>
        <th>2pm</th>
        <th>3pm</th>
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

<br>

### table에 css 적용하기

일반 text와 마찬가지로 css selector로 tag(tr, td, th)를 주거나 class를 설정해주면 된다.

```css
.border-table th, .border-table td {
  border: 1px solid black; 
}

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

