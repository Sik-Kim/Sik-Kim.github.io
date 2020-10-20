---
title: "[CSS] CSS Selector"
date: 2020-10-20 09:39:00 -0400
summary: CSS Selector 사용 방법
---

### CSS selector

css selector란 단어뜻 그대로 css 속성을 적용할 대상을 선택 해주는 요소입니다.



### CSS Syntax 구조(Rule Set)

![css selector](https://www.w3schools.com/css/selector.gif)



CSS Selector는 tag나 attribute(속성)에 따라 표현법이 다릅니다.

1. tag 사용

   > ```css
   > p {
   >   font-size: 20px;
   > }
   > ```

2. class명(attribute) 사용

   > ```css
   > .p-tag {
   >   color: gray;
   > }
   > ```

3. id명(attribute) 사용

   > ```css
   > #third-line {
   >   text-decoration: underline;
   > }
   > ```
   >
   > 

Selector로 tag + class명 or tag + id명을 결합해서 사용할 수도 있습니다.

p tag 내부의 "p-tag" 이름의 class를 selector로 지정

> ```css
> p.p-tag {
>   color: gray;
> }
> ```



"p" tag 내부의 "third-line" 이름의 id를 selector로 지정

> ```css
> p#third-line {
>   text-decoration: underline;
> }
> ```
>
> 

반대로 class + tag 순서로 selector를 지정할 수도 있습니다.

"pre" 이름의 class 내 span tag를 selector로 지정

> ```css
> .pre span {
>   background-color: yellow;
> }
> ```
>
> 

이런식의 결합을 통해 특정 tag나 속성에만 selector를 지정하는 것이 가능합니다.

좀 더 극단적으로 selector를 만든다면 이런식으로도 작성할 수 있습니다.

"a" 이름의 class 내 div tag 내 "b" 이름의 class 내 "pre" 이름의 class 내 span tag를 selector로 준 것입니다.

> ```css
> .a div .b .pre span {
>   background-color: yellow;
> }
> ```
>
> 

### 결론

위와 같이 tag와 속성의 결합을 통해 다양한 css selector를 만들어 낼 수 있습니다. 자신이 원하는 부분에만 css selector를 지정하는 것은 큰 무리가 없을 것입니다. 하지만 css 작성 이전에 html 구조와 class, id 이름을 직관성있고 규칙성 있게 잘 짜는 것이 간결한 코드를 작성하는데 도움이 될 것입니다.