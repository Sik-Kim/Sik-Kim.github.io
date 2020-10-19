---
title: [HTML] id vs class
date: 2020-10-19 21:00:00 -0400
summary: id와 class를 비교해보자
---


# id vs class

### HTML Tag 속성인 id와 class의 기능과 차이점

id와 class 둘다 태그에 이름을 주는 속성입니다.

**id와 class의 다른점은?**

id : id는 고유한 값이어야 합니다. 즉, id명은 오직 하나만 가질 수 있고 다른 tag라 할지라도 같은명을 중복해서 사용할 수 없습니다.

> <div id="profile">

class : id와 다르게 다른 tag라면 중복하여 사용 할 수 있습니다.

> ```html
> <div class="content-wrap"></div>
> <p class="content-wrap"></p>
> ```


<br>
### id와 class에 css 적용하는 법

**Id: #id명**

> ```css
> #profile {
>   border-width: 1px;
>   border-color: black;
>   border-style: solid;
>   text-align: center;
> }
> ```



**Class: #class명**

> ```css
> .content-wrap{
>   background-color: yellow;
>   margin : 0 auto;
> }
> ```

