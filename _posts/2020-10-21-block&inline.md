---
title: "display 속성: block, inline, inline-block의 차이"
date: 2020-10-21 14:00:00 -0400
summary: css block, inline, inline-block 차이점에 대해 알아보자.
---

### display의 속성
display property 속성 중 자주 쓰는 block, inline 차이점과 두 속성의 단점을 보안해주는 inline-block에 대해 알아보겠습니다.

**display: block**  
박스 형태로 기본적으로 width값이 100%가 되며 줄바꿈이 되는 특성이 있습니다.  
width 값을 지정 해
 * width/height/margin/padding 지정 가능
 * block tag 요소
  <address>, <article>, <aside>, <blockgquote>, <canvas>, <dd>, <div>, <dl>, <hr>, <header>, <form>,<h1>, <h2>, <h3>, <h4>, <h5>, <h6>, <table>, <pre>, <ul>, <p>, <ol>, <video>

**display: inline**  
block과 다르게 컨텐츠의 영역 만큼만 요소를 차지하게 되며 줄바꿈을 하지 않습니다. 주로 text에 쓰이며 span tag와 많이 사용합니다.
 * width/height 적용 불가
 * margin/padding의 top, bottom 적용 불가
 * line-height 적용 어려움
 * inline tag 요소
  <a>, <i>, <span>, <abbr>, <img>, <strong>, <b>, <input>, <sub>, <br>, <code>, <em>, <small>, <map>, <textarea>, <label>, <sup>, <q>, <button>, <cite>


**display: inline-block**  
기본적으로 inline과 성질이 비슷한데 block처럼 영역을 지정하는 css 설정들이 모두 적용 가능합니다.  
text 등에 좀 더 디테일한 속성을 적용할 때 inline 대신 inline-block을 지정하는 경우가 많습니다.

 * inline tag 요소
  <img> 등

