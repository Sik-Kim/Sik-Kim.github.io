---
title: "MySQL ⎜ Mysql에서 csv로 추출(Export) 하기(Mac)"
date: 2020-12-31 01:25:00 -0400
summary: 
toc : ture
toc_sticky: true
comments: true
tag : 
    - python
    - mysql
    - csv
    - SQL
category : 
    - database
---

## Intro
백엔드 개발자라면 database 관련 작업은 필수인 것 같다.  
이번 프로젝트에는 데이터 사이언티스트분이 계셔서 필요한 모든 데이터를 제공해주기로 했다.  
그렇다고 데이터 관련 할일이 없는 것은 아니였다.  
필요한 데이터에 대해 데이터 사이언티스트와 협의하고 데이터를 가공하고 DB화 시키는건 백엔드 개발자의 몫이다.

데이터 사이언티스트분과 협업이 가장 중요한 것 같다.  
필요한 데이터를 미리 정확하게 알려주고 협의가 필요한 사항은 즉시 협의했다.  
불필요한 작업을 피하고 필요한 데이터에 최대한 근접한 데이터를 수집할 수 있었다.  

그럼에도 최종 DB화를 위해서 데이터를 가공할 일이 많았다.  
데이터 작업을 하면서 경우에 따라 사용할 수 있는 자동화 코드를 하나씩 구축하는 것도 재밌을 것 같았다.  

데이터 작업 중 mysql의 database를 csv로 추출하는 작업이 필요했는데 생각보다 신경쓸게 많아 정리했다.

<br>

## Mysql의 Database를 csv로 추출하는 방법

Mysql에서 Database를 csv 파일로 추출하는 방법을 정리해보자.  
csv파일로 export 하는 SQL문은 아래와 같다.


### mysql csv export SQL 문
```
SELECT * FROM 테이블명               # 추출할 database
INTO OUTFILE '~/mywork/파일명.csv'  # 저장 경로
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
;
```

문제없이 csv파일이 생성되었다면 축하한다.  

**그러나 만약 아래와 같은 오류가 발생한다면 아래와 같이 secure file에 대한 권한을 해제해줘야 한다.**  
아마 처음 이 작업을 했다면 대부분 이 에러가 발생되었을 것이다.

> The MySQL server is running with the --secure-file-priv option so it cannot execute this statement

<br>

## secure file 권한 변경 방법

### [1] secure_file_priv 권한 확인
우선 secure file 권한 설정이 어떻게 되어 있는지 조회해보자.  
아래 두개 명령어 중 아무거나 입력해주면 된다.

> mysql> SELECT @@GLOBAL.secure_file_priv;  

> mysql> SHOW VARIABLES LIKE "secure_file_priv";


그럼 아래와 같이 권한 정보가 나온다.  
이 권한을 변경해줘야 secure file에 접근이 가능하여 database를 export 할 수 있다.  
그럼 어느 위치에서나 접근 권한이 가능한 빈칸으로 변경하자.  
**⎯ 빈칸 : 어느 위치에서나 권한 접근 가능**  
**⎯ NULL : 어느 위치에서나 권한 접근 불가**  
**⎯ 경로 : 해당 경로에서만 권한 접근 가능**  

![secure file](https://i.ibb.co/PDVqySb/image.png)

<br>

### [2] my.cnf 위치 찾기
my.cnf 파일을 찾는 이유는 여기서 권한 설정을 해줄 수 있기 때문이다.  
일단 이 파일의 위치는 터미널에서 아래의 명령어로 찾을 수 있다. (mysql을 설치한 방법에 따라 경로가 다르다. 많은 블로그에서 파일 위치를 찾는법보다 본인 컴퓨터의 위치대로 알려주는 경우가 많아 나도 이 부분에서 해맸었다.)

> mysql --help | grep "Default options" -A 1

<br>

### [3] my.cnf 파일 열고 아래 입력 후 저장  
빈칸을 입력하여 어디 위치에서든 mysql secure file에 접근 권한이 가능하도록 해줄 것이다.

> secure_file_priv=""

![my.cnf](https://i.ibb.co/Tt7rwXv/image.png)

<br>

### [4] mysql 환경 설정에서 configuration file 경로 설정  
mysql을 설치하면 Mac 시스템 환경설정에 mysql 환경설정이 생긴다.  
여기서 Configuration File의 경로를 실제 나의 my.cnf 경로와 동일하게 설정해야 한다.

![mysql 환경설정](https://i.ibb.co/17NWF7S/image.png)

<br>

### [5] mysql 재가동  
이제 mysql을 재가동 해주면 secure file 권한 변경한 것이 적용된다.  

**⎯ mysql 재가동 : mysql.server restart**  
**⎯ mysql 중지 : mysql.server stop**  
**⎯ mysql 시작 : mysql.server start**  


재가동 후 mysql 접속하여 1번에서 말한 방법으로 secure_file_priv 권한을 다시 확인해보자.  
NULL에서 빈칸으로 변경됐다면 성공이다.  
이제 처음 얘기했던 SQL문으로 csv파일을 export 하면 된다.

