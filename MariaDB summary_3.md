# SQL 기본

> 1. select문 기본 형식
> 2. where
> 3. order by 및 limit
> 4. group by 및 having
> 5. 기타 및 join



## 1) select문 기본형식

```mariadb
/*
SELECT * FROM users;
*/ # 여러 줄의 코드를 주석으로 처리
-- 
```

 * MariaDB는 '--' 이후부터 모든 코드와 글을 주석으로 처리한다.
 * 실제로 많이 사용하는 select문 형태는 다음과 같다.
    * select 변수 이름 from 테이블 이름
       * where 조건
      * group by 열 번호, 변수 이름
      * having 조건 : group by에 대해서만 적용
      * order by 열 번호, 변수 이름



## 2) where

```mariadb
select * from employees 
where emp_no > 10005 
and hire_date  in ('1990-01-22', '1991-01-26')
and gender in ('M','F')
and first_name like '%a%';'%s', 's%'
```

 * where 절은 조회하는 결과에 특정한 조건을 설정하여 원하는 데이터만 보고 싶을 때 사용한다.
 * 조건 연산자와 관계 연산자를 잘 조합하면 다양한 조건의 쿼리를 생성할 수 있다.
 * 연속적인 값을 지닌 변수에 대해선 between ~ and를 사용할 수 있지만 이산적인 값을 저장한 변수에 대해선 이 조건을 쓸 수 없다. 이에 IN을 쓸 수 있다.
     * BETWEEN ~ AND문에서 날짜 조건은 문자로 취급한다.
 * Like 연산자는 문자열의 내용을 검색할 때 이용된다.
    * 특정 문자열 주변에 모든 글자를 허용할 때에는 %를, 한 글자만 포함할 때에는 _을 사용한다.
    * %나 _가 검색할 문자열의 맨 앞에 있으면 MariaDB의 성능에 나쁜 영향을 미칠 수 있다.



```python
select * from employees
where hire_date between '1990-01-01' and '1990-12-31'
and gender in ('F')
order by birth_date;

select * from employees
where hire_date between '1990-01-01' and '1990-12-31'
and gender in ('F')
order by birth_date desc;

select * from employees
where hire_date between '1990-01-01' and '1990-12-31'
and gender in ('F')
order by hire_date asc, first_name desc;

select * from employees
where hire_date between '1990-01-01' and '1990-12-31'
and gender in ('F')
order by 6 asc, 3 desc;

select distinct(gender) from employees;
```

 * order by절은 결과물에 대해 영향을 미치지 않지만 결과가 출력되는 순서를 조절한다.
    * 기본 값은 오름차순 asc로 정렬되고, 내림차순을 설정하려면 열 이름 뒤에 desc를 쓴다.
 * order by 뒤에 여러 변수를 입력함으로써 출력된 자료를 여러 기준으로 정렬할 수 있다.
 * 중복된 것을 1개만 출력하는 조건은 DISTINCT이고 select와 변수 이름 앞에 추가한다.



```mariadb
select * from employees limit 5;

select * from employees
where gender in ('M')
order by hire_date
limit 3;

select * from employees
where gender in ('M')
order by hire_date, birth_date
limit 0,2;
```

 * limit은 출력하는 자료의 개수를 제한하며 항상 SELECT 구문에서 가장 마지막에 입력한다.
    * limit n, m은 n 번째에서 m 번째에 해당하는 자료만 출력한다.
       * limit n, m은 limit m offset n과 같다.



```mariadb
SELECT emp_no, SUM(salary) AS SSAL FROM salaries
GROUP BY emp_no
having sum(salary) > 1000000
and emp_no = 10001

select emp_no, round(salary / 12,2) AS salary from salaries

# 각 직원의 월급에 대한 평균 및 부가조건
select emp_no, round(AVG(salary)/12,2) as SAL from salaries
group by emp_no
having SAL > 5000
order by sal desc limit 5

# 직원별로 가장 많이 받은 연봉을 출력
select emp_no, avg(salary),max(salary), min(salary), max(salary) - min(salary) as rs from salaries
group by emp_no

# 직원별 연봉이 바뀐 횟수
select emp_no, count(salary)-1 from salaries
group by emp_no
```

 * group by는 출력하는 자료를 특정 범주로 묶는 역할을 한다. 특정 범주에 속하는 여러 자료 중 첫 번째 정보를 제외하고 나머지는 출력에서 제외되기 때문에 항상 함수와 같이 사용해야 한다.



|     함수명      |             설명             |
| :-------------: | :--------------------------: |
|      AVG()      |             평균             |
|      MIN()      |            최솟값            |
|      MAX()      |            최댓값            |
|     COUNT()     |          행의 개수           |
| COUNT(DISTINCT) | 행의 개수(중복은 1개만 인정) |
|     STDEV()     |           표준편차           |
|   VAR_SAMP()    |             분산             |

 * having은 집계 함수에 대해서 조건을 제한하지만 항상 group by 뒤에 와야 한다.



```mariadb
INSERT into users values ('id01','pwd01','kim');
insert into users (id,pwd,name) values ('id02','pwd02','kim');

insert into items values (null,'pants3',10000,3.2,current_date);
insert into items values (null,'pants4',20000,3.2,current_date);

insert into items values (null,'pants5',20000,3.2,current_date),
(null,'pants6',20000,3.2,current_date),
(null,'pants7',20000,3.2,current_date),
(null,'pants8',20000,3.2,current_date);

update items set price = price * 

INSERT ignore into users values ('id01','pwd01','kim');
INSERT into users values ('id01','pwd01','kim')
on duplicate key update name = 'lee'
```



```mariadb
# 열의 이름 및 데이터 형식 변환
ALTER table items change column REGDEATE REGDATE DATETIME

# 날짜 형식 변환: CAST 함수를 이용하여 필요한 부분만을 출력할 수 있다.
select if cast(regdate as DATE) as DATE from items;

# 범주별 가격의 평균:
select cate, round(avg(price),0) as aprice from items
group by cate
select cate, convert(avg(price),integer) as aprice from items
group by cate
select cate, cast(avg(price) as integer) as aprice from items
group by cate

# 테이블의 자료를 합쳐서 출력하기
select concat(id, name), price from items
select concat(id, name), price, cast(regdate as date) from items
SELECT CONCAT(id,name), price, DATE_FORMAT(regdate,'%Y%m%d') FROM items;

# 제어 흐름 함수
select avg(price) from items
select count(price) from items
select avg(ifnull(price,1)) from items
```

 * 열 이름 및 데이터 형식을 변경하는 구문은 다음과 같다.
   	* AlTER TABLE 테이블 이름 CHANGE COLUMN 현재 대상 열의 이름, 변경할 열의 이름, 데이터 형식
      	* 뒤에 NULL을 추가하면 NULL값을 허용할 수 있다.
 * 일반적으로 사용하는 데이터 형식 변환 함수는 cast와 convert이다.
   	* cast (변수 as 데이터 형식), convert(변수, 데이터 형식)은 결과에서 큰 차이가 없다.
	* 실제 테이블의 자료를 수정하지 않더라도 concat 함수를 이용하여 원하는 형식으로 출력할 수 있다.
 * 제어 흐름 함수 중에선 IFNULL함수를 자주 사용한다.
    * IFNULL(수식1, 수식2) : 첫 번째 인수가 NULL이면 두 번째 인수가 출력되고 그렇지 않으면 반대로 출력된다.
    * 일반적으로 NULL 값은 배제된다. 그러나 IFNULL 함수와 같이 제어 흐름 함수를 사용하면 상황에 따라 탄력적으로 이를 대처할 수 있다.



```mariadb
select *,
	case
		when PRICE <= 10000 then 'low'
		when PRICE <= 20000 then 'middle'
		when PRICE > 20000 then 'high'
	end as 'level' # 출력하는 열의 이름을 지정한다.
from items

# 범주별 금액의 평균을 구하고 이에 대한 상중하를 출력한다.
select *, cast(avg(price) as integer) as aprice,
	case
		when avg(price) <= 10000 then 'low' # 실제 변수를 이용해야 한다.
		when avg(price) <= 20000 then 'middle'
		when avg(price) > 20000 then 'high'
	end as 'level'
from items
group by cate

# c1(반바지), c2(여름바지), c3(겨울바지)
select *, cast(avg(price) as integer) as aprice,
	case # 기존 열에 추가된는 방식으로 출력
		when avg(price) <= 10000 then 'low' # 실제 변수를 이용해야 한다.
		when avg(price) <= 20000 then 'middle'
		when avg(price) > 20000 then 'high'
	end as 'level',
	case
		when cate = 'c1' then '반바지'
		when cate = 'c2' then '여름바지'
		when cate = 'c3' then '겨울바지'
	end as 'title'
from items
group by cate
```

* 실제로 테이블에 존재하지 않지만 출력으로 새로운 열에서 필요한 정보를 계산할 수 있다.
* CASE는 내장 함수가 아니며 연산자로 분류된다.
   * when에 해당하는 사항이 없으면 ELSE 부분이 반환된다.
  * 파이썬과 달리 끝에 쉼표를 달지 않는다.



```mariadb
# format
select name, price, format(price*rate,3) from items 
select name, price, price*rate, format(price*rate,3),
	DATE_FORMAT(regdate,'%Y%m%d')
from items

select left(upper(first_name),3), last_name from employees
select left(upper(first_name),3), lpad(last_name,10,'#') from employees
select left(upper(first_name),3), lpad(last_name,10,'#'), substr(last_name,2,3)
from employees

select emp_no, hire_date, 
		adddate(hire_date,interval 31 day),
		datediff(now(),hire_date),
		period_diff(date_format(now(),'%Y%m'),date_format(hire_date,'%Y%m')),
		year(hire_date),month(hire_date),day(hire_date)
from employees

# 사원의 입사연도별 근무 일수의 평균: 입사연도, 근무일수 평균
select year(hire_date) as 'hyear',
		avg(datediff(now(),hire_date)),
		avg(period_diff(date_format(now(),'%Y%m'),date_format(hire_date,'%Y%m')))
from employees
group by hyear
```

	*  SQLite에서 지나치게 남용해선 안 된다.

 * format, DATE_FORMAT을 사용하면 문자열이 출력된다.
 * DATEDIFF(날짜1, 날짜2) 함수는 날짜1 - 날짜2의 일수 결과를, PERIOD_DIFF(연월1, 연월2) 함수는 연월1 - 연월2의 개월 수를 구한다.



```mariadb
select
	ROW_NUMBER() OVER (order by price desc) AS 'RK', name, price
from items
limit 5

# RANK
select
	RANK() OVER (order by price desc) AS 'RK', name, price
from items
limit 10

# DENSE_RANK()
select
	DENSE_RANK() OVER (order by price desc) AS 'RK', name, price
from items
limit 10

# 범주별 가격의 순위를 부여
select cate,
	ROW_NUMBER() OVER (partition by cate order by price desc),
	name, price
from items

# 범주별 평균의 순위
select cate, avg(price),
	ROW_NUMBER() OVER ()
from items
group by cate

select ROW_NUMBER() OVER(order by avgp desc) AS 'RK',
	cate, AVG(price) AS 'avgp'
from items
group by cate
order by 1
```

	* RANK 함수는 기준에 따라 정렬했을 때 값이 같을 때에는 동일한 등수를 부여한다. 이후에는 비순차적으로 남은 등수를 부여한다.

* DENSE_RANK 함수는 RANK 함수와 같이 동일한 값에 대해선 복수의 등수를 부여하지만 이후에는 순차적으로 등수를 부여한다.
* 순위함수로 산출한 결과물도 출력에서는 일종의 변수로 취급한다.





```mariadb
# 피벗 테이블

CREATE TABLE carts(
	userid VARCHAR(10),
	itemid INT(10),
	qt INT(10)
);

ALTER TABLE carts 
ADD CONSTRAINT FK_carts_u FOREIGN KEY (userid) REFERENCES users (ID);
ALTER TABLE carts 
ADD CONSTRAINT FK_carts_i FOREIGN KEY (itemid) REFERENCES items (ID);
ALTER TABLE carts ADD CONSTRAINT PK_carts PRIMARY KEY (userid, itemid);

insert into carts values ('id01',1,10);
insert into carts values ('id01',2,20);
insert into carts values ('id01',3,30);
insert into carts values ('id01',4,20);

insert into carts values ('id02',1,40);
insert into carts values ('id02',2,50);
insert into carts values ('id02',3,20);
insert into carts values ('id02',4,70);

select userid,
	sum(IF(itemid = 1, qt, 0)) as 'item1',
	sum(IF(itemid = 2, qt, 0)) as 'item2',
	sum(IF(itemid = 3, qt, 0)) as 'item3',
	sum(IF(itemid = 4, qt, 0)) as 'item4'
FROM carts
group by userid

select u_id,
	sum(IF(o_cancel = 0, o_allprice, 0)) as 'buy accept',
	sum(IF(o_cancel = 1, o_allprice, 0)) as 'buy refuse',
	sum(o_allprice) AS 'total'
from orders
group by u_id

select u_id,
	sum(IF(YEAR(o_regdate) = '2019',o_allprice,0)) AS '2019',
	sum(IF(YEAR(o_regdate) = '2020',o_allprice,0)) AS '2020',
	sum(IF(YEAR(o_regdate) = '2021',o_allprice,0)) AS '2021',
	sum(o_allprice) as 'total'
FROM orders
group by u_id

# JSON(Dictionary)
select
JSON_OBJECT('id',id,'name',name) AS 'jsondata'
from users
```

 * JSON은 현대 웹과 모바일 응용프로그램 등과 데이터를 교환하기 위한 개방형 표준 포맷을 말한다. 속성과 값으로 쌍을 이루며 구성되어 있으며 파이썬의 사전에 해당한다.
   	* 파이썬의 eval 함수를 이용하여 출력한 값을 사전으로 변환한 후에 원하는 값을 출력할 수 있다.



```mariadb
select * from dept;
select * from title;
select * from emp;

# 사장은 출력되지 않는다.
select * from emp e INNER JOIN dept d
ON e.deptno = d.deptno

SELECT e.*,d.deptname,d.deptloc FROM emp e INNER JOIN dept d
ON e.deptno = d.deptno

SELECT * FROM emp e 
INNER JOIN dept d
ON e.deptno = d.deptno
INNER JOIN title t
ON e.titleno = t.titleno

SELECT e.*,d.deptname,d.deptloc FROM emp e 
LEFT OUTER JOIN dept d
ON e.deptno = d.deptno

SELECT e.*,d.deptname,d.deptloc FROM emp e 
RIGHT OUTER JOIN dept d
ON e.deptno = d.deptno

SELECT e.*,d.deptname,d.deptloc FROM emp e 
LEFT OUTER JOIN dept d
ON e.deptno = d.deptno
UNION
SELECT e.*,d.deptname,d.deptloc FROM emp e 
RIGHT OUTER JOIN dept d
ON e.deptno = d.deptno
```

 * 조인은 두 개 이상의 테이블을 서로 묶어서 하나의 결과 집합으로 만들어 내는 것이다.
   	* 데이터베이스의 테이블은 중복과 공간 낭비를 피하고 데이터의 무결성을 위해 여러 개의 테이블로 분리하여 저장한다. 분리된 테이블은 서로 관계를 맺고 있다.  
 * SELECT 열 목록 FROM 첫 번째 테이블
   	* INNER JOIN 두 번째 테이블
      	* ON 조인할 조건(외래 키)
   	* WHERE/GROUP BY/HAVING 부가조건
 * INNER JOIN을 사용했을 때 발생할 수 있는 문제점은 두 개 이상의 테이블의 외래 키에 포함되지 않는 정보는 출력되지 않는다는 것이다. 만약 이 정보가 아주 중요할 때 문제가 발생할 수 있으므로 INNER JOIN과 OUTER JOIN에 대한 출력결과를 비교하는 과정이 필요하다.
 * INNER JOIN에서 나오지 않는 정보도 출력할 수 있게 하는 것이 OUTER JOIN이다.
   	* OUTER JOIN은 양쪽뿐만 아니라 한쪽에만 내용이 있으도 당연히 출력한다.
 * SELECT 열 목록 FROM 첫 번째 테이블[LEFT 테이블]
    * LEFT/RIGHT JOIN 두 번째 테이블[RIGHT 테이블]
    * ON 조인할 조건(외래 키)
    * WHERE/GROUP BY/HAVING/ORDER BY 부가조건
 * LEFT OUTER JOIN은 왼쪽 테이블을 기준으로 하며 이 테이블의 데이터를 모두 출력해야 한다.
    * RIGHT OUTER JOIN은 오른쪽 테이블을 기준으로 하며 이 테이블의 데이터를 모두 출력해야 한다.
    * FULL OUTER JOIN은 LETF OUTER JOIN과 RIGHT OUTER JOIN을 합친 것으로 양쪽 모두에 조건이 일치하지 않는 것을 모두 출력한다.
       * LETF OUTER JOIN과 RIGHT OUTER JOIN을 모두 입력하고 두 문구 사이에 UNION을 입력하면 FULL OUTER JOIN과 동일한 효과가 나타난다.

 

```mariadb
CREATE VIEW vemp
AS
select e.empno, e.name, d.deptname, t.titlename
from emp e
INNER JOIN title t
ON t.titleno = e.titleno
INNER JOIN dept d
ON d.deptno = e.deptno

SELECT * FROM vemp
```

 * 뷰는 일반 사용자 관점에서 테이블과 동일하게 사용하는 개체이다. 
   	* 뷰의 실체는 SELECT문이 되고 쉽게 데이터를 조회하는 것을 목적으로 한다. 그러나 물리적으로 데이터가 들어있는 것은 아니다.
    * 원 테이블에서 데이터를 추가하거나 수정하면 뷰에 이 사실이 반영된다.
      	* 뷰를 통해서 원 테이블의 데이터를 수정할 수 있지만 바람직하지는 않다.
      	* 실시간으로 바뀌는 데이터에는 뷰가 적절하지 않다.
	* 뷰를 통해 중요한 정보가 외부에 유출되지 않게 할 수 있어 보안에 도움이 된다.
	* 뷰를 통해 복잡한 쿼리를 단순하게 다룰 수 있다.



```mariadb
select * from emp
select * from title
select * from dept

# 1. 사원 정보를 조회한다.
	# 사원의 모든 정보와 사원의 상사 이름까지 조회한다.

select a.*, b.name AS '직속상관'
from emp a
LEFT OUTER JOIN emp b
ON a.manager = b.empno

select a.*,
	case
		when a.name = '킹' then '없음'
		else b.name
	end
from emp a
LEFT OUTER JOIN emp b
ON a.manager = b.empno

SELECT A.*, IFNULL(B.empname,'없음') AS 'managername'
FROM emp A
LEFT OUTER JOIN emp B
ON A.manager = B.empno

# 2. 사원 정보를 조회한다.
	# 사원의 모든 정보와 사원의 상사 이름까지 조회한다.
	# 사원의 부서와 타이틀 정보까지 조회한다.

select a.*, d.deptname, t.titlename,b.name AS '직속상관'
from emp a
LEFT OUTER JOIN emp b
ON a.manager = b.empno
LEFT OUTER JOIN dept d
ON a.deptno = d.deptno
LEFT OUTER JOIN title t
ON a.titleno = t.titleno

select a.*, d.deptname, t.titlename,
	case
		when a.name = '킹' then '없음'
		else b.name
	end
from emp a
LEFT OUTER JOIN emp b
ON a.manager = b.empno
LEFT OUTER JOIN dept d
ON a.deptno = d.deptno
LEFT OUTER JOIN title t
ON a.titleno = t.titleno

select a.*, d.deptname, t.titlename, IFNULL(b.name,'없음') AS '직속상관'
from emp a
LEFT OUTER JOIN emp b
ON a.manager = b.empno
LEFT OUTER JOIN dept d
ON a.deptno = d.deptno
LEFT OUTER JOIN title t
ON a.titleno = t.titleno
```

 * 자체 조인은 별도의 구문이 있는 것이 아니라 자신에 조인한다는 것을 뜻한다. 
   	* 조직도와 관련한 테이블에서 주로 사용한다.
    * SELECT 열 목록
      	* FROM 테이블 이름 A
       * LEFT OUTER JOIN / INNER JOIN 테이블 이름 B
       * ON A.상위 카테고리 변수 = B.전체 카테고리 변수
    * WHERE / GROUP BY / HAVING / ORDER BY를 추가할 수 있다.

