# MariaDB
- DDL(Data Definition Language)
    > DB 객체(테이블, 뷰, 함수, 트리거 등)를 생성, 변경, 삭제하는 SQL 명령이다.
- DQL (Data Query Language)
    > 데이터 조회 언어 :: Query : 조회
- DML (Data Manipulation Language)
    > 데이터 조작 언어 :: DQL+DML 묶어서 DML이라고도 한다.

---
### 0. Commands
#### Basic
- `alter user 'root'@'localhost' identified by '1111';` : mysql root 암호 변경
- `CREATE USER '사용자아이디'@'원격호스트주소' IDENTIFIED BY '암호';` : mysql 사용자 추가
- `CREATE USER 'study'@'localhost' IDENTIFIED BY '1111';` : `로컬`에서만 사용할 수 있는 `study`사용자 추가 및 `1111`비밀번호 설정.
- `select user, host from mysql.user;` : mysql 사용자 목록 조회
- `DROP USER 'study'@'localhost';` : mysql 사용자 삭제
- `CREATE DATABASE (DB name) CHARACTER SET utf8 COLLATE utf8_general_ci;` : mysql DB생성
- `DROP DATABASE (DB name);` : mysql DB 삭제
- `GRANT ALL ON (DB name).* TO '사용자아이디'@'서버주소';` : mysql 사용자에게 DB 사용권한 부여
- `show databases;` : DB 목록 조회
- `quit` or `exit` : mysql 프로그램 종료
- `mysql -u study -p` : 'study'사용자로 mysql 접속
- `use (DB name)` : 기본으로 사용할 DB 지정하기 (java의 import와 동일기능)
- `show tables;` : DB의 전체 테이블 목록 조회
- `create view worker as select no, name, class from test1 where working = 'Y';` : `as`이후의 `select`결과를 보여주는 view를 생성한다.
- `drop view 뷰명;` : view 삭제하기


---
#### DDL(Data Definition Language)
- **`create table 테이블명 (컬럼명 타입 NULL여부 옵션, 컬럼명 타입 NULL여부 옵션, ...);` : 테이블 생성 기본형**
    > NULL 여부 : NULL 여부에 대한 언급이 없으면 기본으로 NULL값을 허용한다.

- `describe 테이블명;` = `desc 테이블명;` : 테이블 정보 보기
- `drop table 테이블명;` : 테이블 삭제하기
- `insert into 테이블명(컬럼명, 컬럼명, ...) values(값, 값, ...);` : 테이블의 각 컬럼에 대응하는 값 삽입하기
- <옵션> `default 값` : `insert`시 값 입력을 생략하면 지정된 기본값이 대신 입력된다.
    > 값 입력시 'NULL'을 넣으면 기본값이 입력되지 않고 'NULL'이 들어간다.
- 주석달기 : `comment` 명령어로 입력한 주석은 DBMS에 저장된다.
```sql
CREATE TABLE stnt (
    mno    INTEGER     NOT NULL COMMENT '수강생번호', -- 수강생번호
    work   CHAR(1)     NOT NULL COMMENT '재직여부', -- 재직여부
    acc_no VARCHAR(20) NULL     COMMENT '통장번호', -- 통장번호
    bank   VARCHAR(50) NULL     COMMENT '은행명' -- 은행명
)
COMMENT '수강생';
```


---
###### table 정보 수정
- `primary key`와 `alternate key` 설정
```
alter table test1
    add constraint test1_pk primary key(no),
    add constraint test1_uk unique (name, age);
```

- 테이블에 컬럼 추가
```
alter table test1
  add column no2 int,
  add column age2 int;
```

- 테이블에 인덱스 추가
```
alter table test1
  add fulltext index test1_name_idx (name);
----------------------------------------------
CREATE UNIQUE INDEX UIX_stnt -- 인덱스명
    ON stnt ( -- 테이블명
        acc_no ASC, -- 통장번호 오름차순
        bank ASC    -- 은행명 오름차순
    );
// unique 키와 인덱스 동시에 추가
// unique를 생략하여 인덱스만 추가 가능
```

- 컬럼에 옵션 추가
```
alter table test1
  modify column name varchar(20) not null,
  modify column age int not null,
  modify column kor int not null,
  modify column eng int not null,
  modify column math int not null,
  modify column sum int not null,
  modify column aver float not null;
```

- 특정 컬럼의 값을 자동으로 증가하게 설정한다.
    - <옵션> `auto_increment`
    - 단 반드시 key(primary key 나 unique)여야 한다.
    - 마지막으로 입력된 값을 기준으로 증가한다. (1, 2, 100 순서라면 101)
```
alter table test1
  modify column no int not null auto_increment;
```

- **컬럼에 FK 추가하기**
```
alter table 테이블명
    add constraint 제약조건이름 foreign key (컬럼명) references 테이블명(컬럼명);
```

---
###### key
- `primary key 지정`
    - <옵션> `primary key`
    - <선언> `constraint 키명 primary key(컬럼명)`
```
create table test1(
    name varchar(20) primary key,
    kor int,
    eng int,
    math int
  );
------------------------------------------------------
create table test1(
    name varchar(20),
    age int,
    kor int,
    eng int,
    math int,
    constraint test1_pk primary key(name, age)
  );
```
- `alternate key 지정 (대안키)`
    - <선언> `constraint 키명 unique(컬럼명)`
```
create table test1(
    no int primary key,
    name varchar(20),
    age int,
    kor int,
    eng int,
    math int,
    constraint test1_uk unique (name, age)
  );
```

---
###### index
- name 컬럼을 기준으로 오름차순 정렬
    - <선언> `fulltext index 인덱스명 (컬럼명)`
```
create table test1(
    no int primary key,
    name varchar(20),
    age int,
    kor int,
    eng int,
    math int,
    constraint test1_uk unique (name, age),
    fulltext index test1_name_idx (name)
  );
// fulltext 옵션은 text계열의 타입만 정렬한다.
```

---
#### DML(Data Manipulation Language)
- `insert into 테이블명(컬럼명, 컬럼명, ...) select절;` : select절의 결과를 insert한다.
- `update 테이블명 set 컬럼명=값, 컬럼명=값, ... where 조건...;` : 등록된 데이터를 변경한다.
조건을 지정하지 않으면 모든 데이터를 변경한다.
- `delete from 테이블명 where 조건;` : 데이터를 삭제한다.
조건을 지정하지 않으면 모든 데이터를 삭제한다.
- `set autocommit=false;` & `set autocommit=true;` : 명령창의 내용이 즉시 table에 commit되는 기능을 끄거나 켠다.
- `commit;` & `rollback` : commit=false 상태에서 작업내용을 적용하거나 마지막 commit 시점으로 되돌린다.
- `DROP TABLE IF EXISTS 테이블명 RESTRICT;` : 만약 테이븖이 존재한다면 그 테이블을 삭제한다.
<옵션> : `restrict` : 만약 테이블이 참조당하고 있을 경우 변경/삭제가 취소된다.


---
#### DQL(Data Query Language)
- `select * from 테이블;` : **테이블의 데이터 조회 기본형**
- `select no, concat(name,'(',class,')') from test1;` : 가상의 컬럼 값을 조회하기
출력 데이터형식 : no, name(class) 
- `select 컬럼명 [as] 별명 ...` : 컬럼에 별명 붙히기
as를 생략할 수 있다.
- `select * from test1 where class like 'java%';` : 포함된 문자를 찾는다. `like`
중간의 문자를 찾는 것은 index를 걸어도 조회속도가 느리다.


###### 실행순서 : **from - where - group by - having - select - order by**
###### select
- `select 옵션 컬럼명 as 별명 from 테이블명;` : 테이블에서 컬럼들을 추출하여 보여준다.
    - <옵션> `all` : 중복값을 무시하고 모든 데이터를 추출한다. 생략가능
    - <옵션> `distinct` : 중복값이 있으면 한 개만 추출한다.
    - <옵션> `as 별명` : 각 컬럼에 대한 별칭을 지정할 수 있다. `as` 생략가능

###### where
- `select 컬럼명 from 테이블명 where 조건문` : 조건문을 통과한 데이터들을 추출하여 보여준다.

###### order by
- `select 컬럼명s from 테이블명 order by 컬럼명o 옵션` : 컬럼명o에 따라 오름차순 정렬한다.
    - `컬럼명o` : select절에 있는 컬럼 or 테이블에 존재하는 컬럼이어야 한다.
    - <옵션> `asc` : 오름차순 정렬
    - <옵션> `desc` : 내림차순 정렬

###### union
- `select절1 union select절2` : select절1 과 select절2 의 결과의 합집합을 추출한다.
- `select절1 union all select절2` : select절1과 select절2 의 결과를 중복제거 없이 모두 보여준다.
- `select 컬럼명 from 테이블명 where not 컬럼명 in (select절2)` : select절의 결과 중 select절2의 결과를 뺀 차집합을 보여준다.
- `select 컬럼명 from 테이블명 where 컬럼명 in (select절2)` : select절의 결과와 select절2의 결과의 교집합을 보여준다.

###### join : 너무 방대하여 command 형식 생략
- **< cross join >**
`select * from test1 cross join test2;` : test1의 각 데이터를 test2의 각 데이터와 모두 연결한다. `cross`는 생략가능하다.
- **< natural join >**
`select * from test1 natural join test2 using (aa);` : test1과 test2의 컬럼 중 같은 이름을 가진 컬럼을 기준으로 레코드를 연결한다. 하나뿐이라면 `using (같은 이름을 가진 컬럼명)`을 생략가능하다.
- **< inner join >**
`select * from test1 inner join test2 on 조건절` : 조건절을 통과한 test2의 데이터 중 from절에 있는 테이블과 비교하여 join한다. select구의 컬럼추가에는 제한이 없다. `inner`는 생략가능
- **< outer join >**
`selec * from test1 left outer join test2 on 조건절` : `inner join`과 동일하지만 from절의 테이블에 **있는 값**을 기준으로 하여 데이터를 **모두** 출력한다.

###### group by
- `select 컬럼명1 from 테이블명 group by 컬럼명2` : 컬럼명2를 기준으로 그룹으롬 묶는다. 그룹으로 묶인 경우 그 그룹의 첫 번째 데이터값만 출력한다.
    - `group by`를 사용한 경우 그룹 관련 함수를 사용할 수 있다. 그러나 `group by`절에는 사용할 수 없다.

###### having
- `select 컬럼명1 from 테이블명 group by 컬럼명2 having 조건절` : `group by`의 결과에서 최종 결과를 선택할 조건을 지정할 때 `having 조건절`을 사용한다.
    - `조건절`에 사용하는 컬럼은 `컬럼명1` 또는 `컬럼명2`에 사용된 컬럼이어야 한다.
    - `having`절에 집합 함수를 사용할 수 있다.


---
#### 함수
- `count(*)` : 데이터의 갯수를 한 줄에 출력한다. `*` 대신 컬럼명을 넣으면 해당 컬럼의 값이 null이 아닌 데이터만 카운트한다.
- `now()` : 현재 날짜 및 시간
- `curdate()` : 현재 날짜
- `curtime()` : 현재 시간
- `date(컬럼명)` `time(컬럼명)` : 날짜데이터나 시간데이터만 뽑기
- `date_add(날짜데이터, interval 값 단위);` : 날짜데이터에 년월일시분초 등을 추가하거나 빼기
ex) `select date_add(now(), interval 11 day);`
- `datediff(날짜1, 날짜2);` : 두 날짜 사이의 간격 알아내기
ex) `select datediff(curdate(), '2022-5-2');`
- `date_format(날짜, 형식)` : 날짜에서 특정 형식으로 값을 추출하기
ex) `select regdt, date_format(regdt, '%m/%e/%Y') from test1;`
```
<2022년 8월 3일 수요일 오후 1시 33분 45초>
- 년
    %Y : 2022
    %y : 22
- 월
    %M : August
    %m : 08
    %b : Aug
- 일
    %e : 3
    %d : 03
- 주
    %W : Wednesday
    %w : 3 :: 일~토 > 0~6
    %a : Wed
- 시
    %p : PM
    %h : 01
    %H : 13
    %l : 1
- 분
    %i : 33(분)
- 초
    %s : 45(초)
```
- 문자열을 날짜 값으로 바꾸기
    - 날짜 값을 저장할 때 기본 형식은 yyyy-MM-dd이다.
```
select str_to_date('11/22/2022', '%m/%d/%Y');
select str_to_date('2022.2.12', '%Y.%m.%d');
-----------------------------------------------
/* 다음 형식의 문자열을 날짜 값으로 지정할 수 없다.*/
insert into test1 (title, regdt) values('bbbb', '11/22/2022');
-----------------------------------------------
/* 특정 형식으로 입력된 날짜를 date 타입의 컬럼 값으로 변환하면 입력할 수 있다.*/
insert into test1 (title, regdt) values('bbbb', str_to_date('11/22/2022', '%m/%d/%Y'));
```





---
### 1. DDL(Data Definition Language)
- 데이터베이스(database) = 스키마(schema)
- 테이블(table)
- 뷰(view)
- 트리거(trigger=listener)
  - 특정 조건에서 자동으로 호출되는 함수
  - 특정 조건? SQL 실행 전/후 등
  - OOP 디자인 패턴에서 옵저버에 해당한다.
- 함수(function)
- 프로시저(procedure)
- 인덱스(index)

---
#### 컬럼 타입
- int
    - 4바이트 크기의 정수 값 저장
    - 기타 tinyint(1바이트), smallint(2바이트), mediumint(3바이트), bigint(8바이트)
- float
    - 부동소수점 저장
- numeric = decimal
    - 전체 자릿수와 소수점 이하의 자릿수를 정밀하게 지정할 수 있다.
    - `numeric(n,e)` : 전체 n 자릿수 중에서 소수점은 e 자릿수다.
        - 예) numeric(10,2) : 12345678.12
    - `numeric` : `numeric(10, 0)` 과 같다.
- char(n)
    - 최대 n개의 문자를 저장.
    - 0 <= n <= 255
    - 고정 크기를 갖는다.
    - 한 문자를 저장하더라도 n자를 저장할 크기를 사용한다.
    - 메모리 크기가 고정되어서 검색할 때 빠르다.
- varchar(n)
    - 최대 n개의 문자를 저장.
    - 0 ~ 65535 바이트 크기를 갖는다.
    - n 값은 문자집합에 따라 최대 값이 다르다.
    - 한 문자에 1바이트를 사용하는 ISO-8859-n 문자집한인 경우 최대 65535 이다.
    - 그러나 UTF-8로 지정된 경우는, n은 최대 21844까지 지정할 수 있다.
    - 가변 크기를 갖는다.
    - 한 문자를 저장하면 한 문자 만큼 크기의 메모리를 차지한다.
    - 메모리 크기가 가변적이라서 데이터 위치를 찾을 때 시간이 오래 걸린다.
    그래서 검색할 때 위치를 계산해야 하기 때문에 검색 시 느리다.
- **text(65535)**, mediumtext(약 1.6MB), longtext(약 4GB)
    - 긴 텍스트를 저장할 때 사용하는 컬럼 타입이다.
    - 오라클의 경우 long 타입과 CLOB(character large object) 타입이 있다.
- date
    - 날짜 정보를 저장할 때 사용한다.
    - 년,월,일 정보를 저장한다.
    - 오라클의 경우 날짜 뿐만 아니라 시간 정보도 저장한다.
- time
    - 시간 정보를 저장할 때 사용한다.
    - 시, 분, 초 정보를 저장한다.
- datetime
    - 날짜와 시간 정보를 함께 저장할 때 사용한다.
- boolean
    - 보통 true, false를 의미하는 값을 저장할 때는 정수 1 또는 0으로 표현한다.
    - 또는 문자로 Y 또는 N으로 표현하기도 한다.
    - 실제 컬럼을 생성할 때 tinyint(1) 로 설정한다.

---
#### index
- 검색 조건으로 사용되는 컬럼인 경우 따로 정렬해 두면 데이터를 찾을 때 빨리 찾을 수 있다.
- 특정 컬럼의 값을 A-Z 또는 Z-A로 정렬시키는 문법이 인덱스이다.
- DBMS는 해당 컬럼의 값으로 정렬한 데이터 정보를 별도의 파일로 생성한다.
- 보통 책 맨 뒤에 붙어있는 색인표와 같다.
- 인덱스로 지정된 컬럼의 값이 추가/변경/삭제 될 때 인덱스 정보도 갱신한다.
- 따라서 입력/변경/삭제가 자주 발생하는 테이블에 대해 인덱스 컬럼을 지정하면,
  입력/변경/삭제 시 인덱스 정보를 갱신해야 하기 때문에
  입력/변경/삭제 속도가 느려지는 문제가 있다.
- 대신 조회 속도는 빠르다.

---
#### view
- view가 참조하는 테이블에 데이터를 입력한 후 view를 조회하면?
  => 새로 추가된 컬럼이 함께 조회된다.
- 뷰를 조회할 때 마다 매번 select 문장을 실행한다.
  => 미리 결과를 만들어 놓는 것이 아니다.
- 일종의 조회 함수 역할을 한다.
- 목적은 복잡한 조회를 가상의 테이블로 표현할 수 있어 SQL문이 간결해진다.

---
#### Transaction
- 여러 개의 DML 작업을 한 단위로 묶는 것을 가리킨다. `commit 과 rollback 사용`
- 그 단위에 묶인 작업이 모두 성공할 때만 업무가 완성된다.

---
### 2. DQL(Data Query Language)
-  조회할 때 조건 지정하기
    - where 절과 연산자를 이용하여 조회 조건을 지정할 수 있다.
    - 이렇게 조건을 지정하여 결과를 선택하는 것을 "**셀렉션(selection)**" 이라 한다.
- \+ `select 컬럼명 ...` 에서 컬럼명을 **Projection** 대상이라고 한다.

---
#### 연산자
- OR, AND, NOT
    - OR : 두 조건 중에 참인 것이 있으면 조회 결과에 포함시킨다.
    - AND : 두 조건 모두 참일 때만 조회 결과에 포함시킨다.
    - NOT : 조건에 일치하지 않을 때만 결과에 포함시킨다.

- 사칙연산
    - +, -, *, /, % 연산자를 사용할 수 있다.

- 비교연산
    - =, !=, >, >=, <, <=, <>

- between 값1 and 값2
    - 두 값 사이(두 값도 포함)에 있는지 검사한다.
    - ex) `select 5 between 3 and 5;`

- like
    - 문자열을 비교할 때 사용한다.
    - ex) `select * from test1 where class like 'java%';`
    - `%` : 문자가 0개 이상 있다.
    - `_` : 이 자리에 문자가 1개 있다.

---
#### 서브쿼리
- 각 `select절`, `from절`, `where절`,  `컬럼명` 등에 모두 `select절`을 사용할 수 있다.
- 이는 조회성능을 크게 떨어뜨리므로 잘 생각하고 사용할것.

---
### 3. key
![](./img/fig5.png)

- key
  - 데이터를 구분할 때 사용하는 컬럼들의 집합
- candidate key (후보키 = 최소키)
  - key 들 중에서 최소 항목으로 줄인 키
- **primary key (주 키)**
  - candidate key 중에서 DBMS 관리자가 사용하기로 결정한 키
  - 테이블의 데이터를 구분할 때 사용하는 컬럼들이다.
  - 줄여서 PK라고 표시한다.
  - PK 컬럼을 지정하지 않으면 데이터가 중복될 수 있다.
- alternate key (대안 키)
  - candidate key 중에서 primary key로 선택된 키를 제외한 나머지 키.
  - 비록 primary key는 아니지만, primary key 처럼 데이터를 구분하는 용도로 대신 사용할 수 있다고 해서 **대안 키(alternate key)** 라 부른다.
- artificial key (인공키)
    - Primary key로 사용하기에 적절한 컬럼을 찾을 수 없다면, key로 사용할 컬럼을 추가한다.
    - 보통 일련번호를 저장할 정수 타입의 컬럼을 추가한다.
    - 대부분의 SNS 서비스들은 일련의 번호를 primary key 사용한다. 왜?
```
- 회원 탈퇴의 경우,
    - 회원 탈퇴할 때 아이디도 제거한다.
    - 아이디를 지우면 그 아이디와 연결된 게시글을 지워야 한다.
    - 그런데 회원 아이디 대신 일련 번호를 사용하면,
    - 그 회원이 쓴 게시글은 일련번호와 묶인다.
    - 따라서 아이디가 삭제되더라도 해당 글은 계속 유효하게 처리할 수 있다.

    - primary key 값은 다른 데이터에서 사용하기 때문에,
      - 예) 게시글을 저장할 때 회원 이메일을 저장한다고 가정하자.
    - pk 값을 변경하면 그 값을 사용한 모든 데이터에 영향을 끼친다.
    - 그래서 PK 값을 다른 데이터에서 사용한 경우,
      DBMS는 PK 값을 변경하지 못하도록 통제한다.
    - 이렇게 변경될 수 있는 값인 경우, PK로 사용하지 말라.
    - 대신 회원 번호와 같은 임의의 키(인공 키)를 만들어 사용하는 것이 좋다.
```

- FK(Foreign Key)
    - 다른 테이블의 **PK**를 참조하는 컬럼이다.
    - 다른 데이터를 참조하는 경우 해당 데이터의 존재 유무를 검사하도록 강제한다.
    - 다른 테이터에 의해 참조되는지 여부를 검사하도록 강제한다.
    - 다른 테이블의 데이터와 연관된 데이터를 저장할 때 무효한 데이터가 입력되지 않도록 제어하는 문법이다.
    - 다른 테이블의 데이터가 참조하는 데이터를 임의의 지우지 못하도록 제어하는 문법이다.
    - 그래서 데이터의 **무결성**(data integrity; 결함이 없는 상태)을 유지하게 도와주는 문법이다.

- 다른 테이블에 의해 참조되는 테이블을 '**부모 테이블**'이라 부른다.
- 다른 테이블의 데이터를 참조하는 테이블을 '**자식 테이블**'이라 부른다.

---
#### etc.
```
1. < sql의 convention >
    ex) where name='a'
    에서 '='앞뒤로 띄우거나 붙이거나 반반이다. (니맘대로다.)

2. OracleDB는 from절 필수지만 MariaDB는 질의가능
    ex) select (4=5), (4!=5), (4>5), (4>=5), (4<5), (4<=5), (4<>5);
    결과는 각각에 대하여 boolean값을 반환한다.
```

---

---
# JDBC
1. `java.sql.Driver` 구현체 준비
=> JDBC Driver에 대한 정보를 갖고 있다.
2. `java.sql.DriverManager` 에`Driver 구현체` 등록
3. `DriverManage`를 통해 `java.sql.Connection` 객체 얻기
=> DBMS와의 연결정보 갖고있다.`
4. `Connection`을 통해 `java.sql.Statement/PreparedStatement` 객체를 준비시킨다. `<= SQL을 서버에 보내고 응답을 받는 일을 한다.`
5. `Statement/PreparedStatement` 를 통해 `java.sql.Resultset`을 얻는다.
=> DBMS의 select 결과를 한 개씩 가져오는 일을 한다.

- 참고 자료
![](./img/fig10.png)

---
### 0. Commands
- Driver 구현체를 생성하고, 등록하고 DBMS와의 연결을 한번에 한다.
```
java.sql.Connection con = DriverManager.getConnection(
            "jdbc:mariadb://localhost:3306/studydb?user=study&password=1111");
```

- `java.sql.Statement stmt = con.createStatement();` : `ResultSet` 객체를 얻는다. **하지만 Statement 객체는 sql삽입공격에 취약할 수 있다.**
- `stmt.executeUpdate("sql문");` : java에서 DBMS를 향해 데이터를 조작하는 명령을 보낸다.
- `stmt.executeQuery("sql문");` : java에서 DBMS를 향해 데이터조회를 요청하는 명령을 보낸다.
- `rs.next();` : 데이터 조회를 요청하여 레코드를 1개 가져오는데에 성공했다면 true, 아니라면 false를 리턴한다.
- `rs.getXxx(컬럼번호);` : 테이블을 생성할 때 작성한 순서대로 컬럼번호가 매겨진다.
배열과 달리 첫번호는 1번이다.
    - `getInt();` : int, number 
    - `getString();` : char, varchar, text
    - `getDate(), getTime()` : date, time, datetime
    - `getFloat()` : float
- `rs.getString("board_id");` : 컬럼번호 대신 컬럼명을 써서 가독성을 높일 수 있다.

---
###### SQL 공격 차단하기
- `PreparedStatement stmt = con.prepareStatement("insert into x_board(title,contents) values(?,?)")` : `준비된문장` 객체를 얻기위해 `준비하라문장();` 을 이용한다. **이 문법은 sql삽입공격을 차단한다.**
`?`는 값이놓일 자리, **in-parameter** 라 한다.
- `stmt.setString(1, title);` : `1`번째 `?`값에 `값(title)`을 넣는다.

---
### 1. Statement vs PreparedStatement

1. SQL 문장의 간결함
- [Statement]
    - 값을 가지고 문자열로 직접 SQL 문을 만들기 때문에 작성하거나 읽기 힘들다.
- [PreparedStatement]
    - SQL 문장과 값이 분리되어 있기 때문에 작성하거나 읽기 쉽다.
2. SQL 삽입 공격
- [Statement]
    - 사용자가 입력한 값을 가지고 SQL 문장을 만들기 때문에 해킹되기 쉽다.
- [PreparedStatement]
    - SQL 문장과 값이 분리되어 다뤄지기 때문에 해킹할 수 없다.
3. 바이너리 데이터 다루기
- [Statement]
    - 문자열로 SQL 문장을 만들기 때문에 바이너리 타입의 컬럼 값을 설정할 수 없다.
- [PreparedStatement]
    - setXxx() 메서드를 호출하여 값을 설정하기 때문에 바이너리 타입의 컬럼 값을 설정할 수 있다.
4. 실행 속도
- [Statement]
    - executeUpdate()를 실행할 때 SQL 문을 파라미터로 전달한다.
    - 호출될 때마다 SQL 문법을 분석하기 때문에 반복 실행하는 경우 SQL 문법도 반복 분석하므로 실행 속도가 느리다.
- [PreparedStatement]
    - 미리 SQL 문을 작성한 다음 DBMS 프로토콜에 맞게 파싱해 놓은 후, executeUpdate() 호출한다.
    - 따라서 executeUpdate()를 호출할 때 마다 SQL 문법을 분석하기 않으므로 반복해서 실행하는 경우, Statement 보다 실행 속도가 빠르다.