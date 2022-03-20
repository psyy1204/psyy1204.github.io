---
layout: single
title:  "mySQL 명령어 정리"
published: true
categories: DataBase
tag: [DB, mySQL]
toc: true
---

# mySQL 명령어 정리

### 기본 명령어

* DB 접속

```mysql
mysql -u [계정ID] -p;
mysql -u [계정ID] -p [DB 이름];
mysql -u [계정ID] -p [계정PW];
mysql -u [계정ID] -p [계정PW] [DB 이름];
```

* DB 종료

```mysql
exit;
quit;
```

* 기본 정보 확인

```mysql
status;
```

* 에러, 경고 메세지 재확인

```mysql
show errors;
show warnings;
```



### DB 관련 명령어

* DB 생성

```mysql
create database [DB명];
```

* DB 목록 확인

```mysql
show databases;
```

* DB 선택하기

```mysql
use [DB명];
```

* 사용중인 DB 출력

```mysql
select databases();
```

* DB 삭제

```mysql
drop database [DB명];
```



### 테이블 관련 명령어

* 테이블 생성

```mysql
create table [테이블 이름]([열 이름] INT(5), [열 이름] VARCHAR(10), 데이터 타입 참조하기);
create table [테이블 이름](colmun_name1 INT(5), colmun_name2  VARCHAR(10));
```

* 테이블 목록 확인

```mysql
show tables;
```

* 테이블 구조 확인

```mysql
explain [테이블 이름];
describe [테이블 이름];
desc [테이블 이름];
show index from [테이블 이름];
```

* 테이블명 변경

```mysql
rename table [테이블명] to [변경할 테이블명];
```

* 테이블 삭제

```mysql
drop table [테이블명];
```



### 데이터 관리 명령어

* 데이터 입력

```mysql
insert into [테이블명] values(값1, 값2, ...);
insert into [테이블명](colmun_name1, colmun_name2, ...) values(값1, 값2, ...);
```

* 데이터 수정

```mysql
update [테이블명] set [열 이름2]='값2' where [열 이름1]='값1';
```

-[테이블 이름]의 [열 이름1] 값이 ‘값1’ 인 데이터의 [열 이름2] 값을 ‘값2’로 수정

-where 조건식이 없으면 해당 열에 모든 값이 바뀜

* 데이터 삭제

```mysql
delete from [테이블명];
//데이터 전체 삭제
delete from [테이블명] where [조건 열]='조건 데이터 값';
//조건에 맞는 데이터 삭제
```

* 데이터 검색

```mysql
select * from [테이블명];
select [필드 이름1, 필드 이름2] from 테이블명;
```

* 데이터 검색 조건

```mysql
select * from 테이블명 where 조건;
select * from [테이블명] where [필드 이름] = '10';
//[필드 이름] 값이 10 인 데이터 검색
select * from [테이블명] where [필드 이름] >'10' and [필드 이름2] <'20';
//[필드 이름] 값이 10 이상, [필드 이름2] 값이 20 이하인 데이터 검색
select * from [테이블명] where [필드 이름] like 'A%';
//A 로 시작하는 데이터 검색
select * from [테이블명] where [필드 이름] like '%A';
//A 로 끝나는 데이터 검색
select * from [테이블명] where [필드 이름] like '%A%';
//A 를 포함하는 데이터 검색
```

* 데이터 정렬

```mysql
select * from [테이블 이름] order by [필드 이름]
//필드 이름 기준 오름차순 정렬
select * from [테이블 이름] order by [필드 이름] desc
//필드 이름 기준 내림차순 정렬
```

* 일정 데이터 출력

```mysql
select * from [테이블 이름] [필드 이름] asc limit [시작 번호], [검색할 데이터 개수];
```



### 유저관련 명령어

* 유저 리스트 보기

```mysql
select user, host from mysql.user;
```

* 유저 추가하기

```mysql
create user '유저ID'@'localhost' identified by '유저암호';
```

* 유저 권한 부여

```mysql
grant all on *.* to '유저ID'@'localhost';
grant all on [DB이름].* to '유저ID'@'localhost';
grant all on [DB이름].* to '유저ID'@'%'; // 외부 호스트
```

* 유저 삭제

```mysql
delete from user where user = '유저ID';
flust privileges;
```