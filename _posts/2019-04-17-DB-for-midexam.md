---
title: "Oracle DB 공부한것 정리"
layout: post
tags: Oracle
comments: true
---

conn 계정명 - 로그인  
show user - user 보여줌  
create user 계정명 identified by 비밀번호 - 계정 생성  
grant connect, resource, dba to 계정명 - 기본 권한 부여

**테이블 생성** 
```
create table 테이블명(
  컬럼명 데이터형식 (제약조건 지정),
  컬럼명 데이터형식2,
);
```

```
#Example
create table comPerson
(
	cno char(6) primary key,
	cname char(20),
	cpos char(20),
	cdept char(3)
);
```

**테이블 삭제**  

`drop table 테이블명;`  
`delete` - 레코드만 삭제

데이터 타입 char, datetime인 속성에 값을 입력할 때는 작은 따옴표로 묶어준다.

```
#Example2
select * from comPerson;

insert into comPerson
values ('200401', '김성진', '대리', '총무부');
```

**테이블 값 추가**  

`insert into 테이블명(컬럼명, 컬럼명) values ('추가할 값', 추가할 값);`

```
#Example3
insert into st1(stno, stname, stdept, stage, stcity)
values ('st101', '김유선', '전산과', 20, '인천');
```

**테이블 수정**

`alter table 테이블명 add(추가할 컬럼명 데이터타입);`

```
#Example4
Alter table player
add (address varchar2(80));
```

`alter table 테이블명 modify (컬럼명1 데이터 유형);` - 데이터 유형 변경시 

`Alter table 테이블명 rename column 변경해야 할 칼럼명 to 새로운 칼럼명;`

```
#Example5
Alter table player
rename column player_id to temp_id;
```

**테이블 수정 Update**

`update 테이블명 set 수정되어야 할 컬럼명 = 수정되기를 원하는 새로운 값;`

```
#Example6
update st1
set stage = stage+1;
```

`desc 테이블명` - Table type 등 확인 할 때 사용

`truncate table 테이블명;` - 테이블에 들어있던 모든 행들이 제거된다.  
이 경우에는 Auto Commit을 하기 때문에 복원이 불가능하므로 복원이 필요하면 Delete를 사용해야 한다.

---

**190403**

@C:\Users\PC\Downloads\1.soccer.sql -> 특정 경로에 위치한 SQL 데이터 한번에 넣는다.

`Order by` - 순서대로 정렬한다.
`Count` - 특정 조건을 만족하는 열의 숫자를 반환한다.
`Distinct` - 중복없이 값을 보여준다.

```
#Example7
select '전체 개수:', count(*), '점수 입력 개수:', count(rgscore)
from register;
```

`avg` - 평균
`max` - 최고점
`min' - 최저점

```
#Example8
select avg(rgscore), max(rgscore), min(rgscore)
from register;
```

`group by` - 데이터들을 원하는 그룹으로 나눌 수 있다.
`group by` 사용시 select에도 group by에서 사용한 <속성>과 집계함수만 나올수있다.

```
#Example9 - 학생 테이블로부터 stdept 별로 학생의 수를 구하라
select stdept, count(*)
from student
group by stdept;
```

`having` - 집계함수를 가지고 조건비교를 할 때 사용한다.

```
#Example10
select stno, avg(rgscore)
from register
group by stno
having avg(rgscore) >= 85;
```

상호 조인 = 카티션 프로덕트
속성 개수는 + -> ex) 3 + 3 = 6
튜플 개수는 x -> ex) 4 x 2 = 8

```
#Example11
select student.*, register.*
from student, register;
```

내부 조인

```
#Example12
select student.*, register.*
from student, register
where student.stno = register.stno;
```

**조인 예제 문제**
```
#Example13 - 우지민 학생이 수강신청한 과목의 stname, sbno, rgscore를 검색하라
select student.stname, register.sbno, register.rgscore
from student inner join register on student.stno = register.stno
where student.stname = '우지민';
```

---

**190410**

`Constraint` - 부적절한 자료가 입력되는 것을 방지하기 위해서 여러가지 규칙을 적용해 놓는 것
테이블 생성 후 ALTER 명령어를 통해 추가 가능하다.

`Check` - 컬럼에 입력할 값이 정해져 있는 경우 사용

`Sequence` - Unique한 값을 연속적으로 생성하기 위해 사용

`NVL` - NULL 값을 다른 값으로 대치하여 연산하거나 다른 값으로 출력

```
select stname "이름", NVL(telephone1, '연락처없음') "전화번호" from student;
```

`rownum` - SQL 조회 결과의 순번을 나타낸다. 자료의 일부분만 확인하여 처리할 대 유용하다.

```
#Example14 - 수강 목록에서 학번, 과목번호, 날짜를 앞의 두 명만 보이시오.
select rownum "순번", stno, sbno, rgdate
from register
where rownum <= 2;
```

**중첩질의**
190417

```
#Example15
select stno, rgscore
from register
where sbno = (select sbno, from subject, where sbname='C언어');
```

```
#Example16 - stno sb606을 수강신청한 학생의 stname을 중첩된 질의어를 사용하여 검색
select stname
from student
where stno in (select stno from register where sbno ='sb606');
```

```
#Example17 - 전체 수강신청 정보에서 stno, sbno, rgscore 점수에서
평균 점수를 뺀 값을 중첩된 질의어를 사용하여 검색

select stno, sbno, rgscore, rgscore-(select avg(rgscore) from register)
from register;
```

```
#Example18 - 부질의에서 인천에 거주하는 학생의 전체 정보를 검색하고,
이 결과와 수강신청 테이블을 조인하여 해당 학생의 stno, stname, sbname, rgscore를 검색

select reg.stno, ins.stname, reg.sbno, reg.rgscore
from (select * from student where stcity='인천') ins
inner join register reg on ins.stno = reg.stno;
```

```
#Example19 - 학번이 st101인 학생이 한 과목이라도 수강신청을 하였다면
해당 학생의 stno, stname, stdept 검색

select stno, stname, stdept
from student
where stno = 'st101'
and exists(select * from register where stno = 'st101');
```


