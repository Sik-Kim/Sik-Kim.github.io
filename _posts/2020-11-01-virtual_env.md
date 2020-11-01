---
title: "파이썬 가상환경 구축하기(Miniconda 설치)"
date: 2020-11-01 20:49:00 -0400
summary: 파이썬 가상환경, conda 명령어 익히기
toc : ture
tag : miniconda conda env pip 가상환경
category : miniconda
---

# 파이썬 패키지 설치 프로그램

## PIP? 아나콘다? 미니콘다? Pipenv?

파이썬 입문 후 처음 환경 세팅할 때 패키지 설치 프로그램 때문에 한번은 혼란스러울 것이다.

## 왜 필요한가요?
프로그램은 파이썬에 프레임워크/라이브러리를 설치를 위한 프로그램이다.

## 종류가 많아서 뭘 쓸지 모르겠어요.
파이썬을 설치하면 기본적으로 PIP이 자동으로 설치되어 있다. 입문자일 경우 당분간은 PIP을 사용해도 큰 문제가 없을 것이다. 그리고 불행히도 파이썬 공식 홈페이지에서도 친절하게 pip으로 설치하는 것을 안내해주고 있다.

[https://pypi.org/project/Django/](https://pypi.org/project/Django/)
![pip_install](https://i.ibb.co/Ltm287Z/pipinstall.png)


## 그럼 뭘 써야 하나요?
일단 결론부터 얘기하면 PIP 사용은 추천하지 않고 pipenv나 miniconda 사용을 추천한다. **그 이유를 알려면 가상환경에 대한 개념을 이해해야 한다.**

<br><br>

# 가상환경(Virtual Environments)
가상환경은 파이썬의 프로젝트마다 독립적으로 가상의 환경에 모듈을 설치하는 것이다.  

쉽게 생각해보자. 가상환경을 Bubble에 비유하기도 한다. **Bubble과 같이 분리된 각각의 Project에 적용되는 각각의 가상환경이 있는 것이다.**  
반면 PIP으로 모듈을 설치하면 프로젝트 구분없이 파이썬에 Global하게 설치가 되어 프로젝트별로 모듈이 동일하게 설치되며 여러 프로젝트를 진행하거나 장기간 프로젝트를 할 시 모듈의 Version이 꼬일 가능성이 높다.

![bubble](https://i.ibb.co/6mRWZN7/bubble.png)

<br><br>

# Miniconda 설치
나의 경우에는 miniconda를 사용해서 가상환경을 구축했다. Anaconda는 용량만 해도 10GB가 넘고 무겁기 때문에 사용하지 않았다. 아래 링크에서 다운 받을 수 있다.
[https://docs.conda.io/en/latest/miniconda.html](https://docs.conda.io/en/latest/miniconda.html)

설치가 제대로 됐는지 확인은 terminal에서 conda라고 검색하면 conda에 대한 command 항목이 나오는 것을 확인할 수 있다.

<br><br>

# conda 가상환경 만들기(명령어)

그럼 conda를 통해서 my_test_env라는 가상환경을 만들고 django 설치까지 진행해보겠다.

1) conda 가상환경 만들기(여기서는 test_env라는 이름의 가상환경을 만든다.)
> conda create -n test_env python=3.7

![conda1](https://i.ibb.co/h8y2kjc/move1.gif)

<br>

2) 가상환경 확인
설치된 모든 가상환경 리스트를 보여준다.
> conda env list

![conda2](https://i.ibb.co/T4kh9Wm/condaenvlist.png)

<br>

3) 만든 가상환경 실행하기
가상환겨을 실행하면 Terminal에서 (base)로 되어 있던 것이 (test_env)로 바뀌는 것을 확인 할 수 있다.
> conda activate test_env

![conda3](https://i.ibb.co/gWv10zx/condaactivatetestenv.png)

<br>

4) 프레임워크(장고) 설치
> pip install django

![conda4](https://i.ibb.co/TkGKRVh/pipinstalldjango.png)

<br>

5) 설치 확인
> pip freeze

![conda5](https://i.ibb.co/KKb9QCp/pipfreee.png)

무사히 가상환경 세팅 및 장고 설치까지 해봤다. 1개의 프로젝트에 1개의 가상환경이 있는 것이며, 프로젝트명과 가상환경명을 동일하게 하는걸 추천드린다. 프로젝트 시작할때는 일단 가상환경부터 키고 시작하는 것이라 생각하면 된다.

<br>

6) 가상환경 종료
> pip deactivate

<br>

7) 가상환경 삭제
기본적으로 가상환경 삭제는 명령어를 사용하면 된다.
예를 들어 위에서 만든 test_env 가상환경을 삭제하려면 아래와 같은 명령어를 입력하면 된다.
> conda env remove -n test_env

하지만 가끔 위 명령어로 삭제되지 않을때도 있다. 그럴땐 권장하는 방법은 아니지만 conda env list로 확인가능한 경로를 따라가서 직접 삭제하는 방법도 있긴하니 조심히 사용하길 바란다.