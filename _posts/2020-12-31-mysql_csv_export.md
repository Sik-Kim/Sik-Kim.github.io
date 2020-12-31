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
category : 
    - database
---

## Mysql의 Database를 csv로 추출하는 방법

Mysql에서 Database를 csv 파일로 추출하는 방법을 정리해보자.  
처음 해보는 것이라면 아마 secure file 접근 권한을 풀어줘야 할 것이다.  

우선 csv export가 되는지 시도해보자.

### mysql csv export SQL 문
```
SELECT * FROM 테이블명
INTO OUTFILE '~/mywork/파일명.csv' # 저장 경로
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
;
```

위와 같은 SQL문 입력시 권한 문제가 없다면 문제없이 csv파일이 생성됐을 것이다.  
만약 아래와 같은 오류가 발생한다면 아래와 같이 secure file에 대한 권한을 해제해줘야 한다.  

> The MySQL server is running with the --secure-file-priv option so it cannot execute this statement

<br>

secure file 권한 변경 방법

우선 secure file 권한 설정이 어떻게 되어 있는지 조회해보자.
아래 두개 명령어 중 아무거나 입력해주면 된다.

> mysql> SELECT @@GLOBAL.secure_file_priv;  

> mysql> SHOW VARIABLES LIKE "secure_file_priv";

