```mariadb
CREATE TAbLE title(
	titleno CHAR(2) PRIMARY KEY,
    titlename NVARCHAR(20)
);

CREATE TAbLE dept(
	deptno CHAR(2) PRIMARY KEY,
    deptname NVARCHAR(20),
    deptloc NVARCHAR(20)
);

CREATE TAbLE emp(
	empno CHAR(4),
    titleno CHAR(2),
    deptno CHAR(2),
    name NVARCHAR(10),
    manager char(4),
    salary INT(5),
    hdate DATE
);

ALTER TABLE emp
ADD CONSTRAINT fk_title FOREIGN KEY (titleno) REFERENCES title (titleno);
ALTER TABLE emp
ADD CONSTRAINT fk_dept FOREIGN KEY (deptno) REFERENCES title (deptno);

INSERT INTO title VALUES
('10','사원'),('20','대리'),('30','과장'),('40','사장'),('50','인턴');
INSERT INTO dept VALUES
('10','본사','서울'),('20','영업부','경기'),('30','생산부','부산'),('40','연구소','대전');
INSERT INTO emp VALUES
('1001','40','10','킹',NULL,5000,'1997-01-01'),
('1002','30','20','이영업','1001',4300,'1998-01-01'),
('1003','30','30','김생산','1001',4800,'1999-01-01'),
('1004','30','40','홍연구','1001',4500,'1999-12-01'),

('1005','20','20','이말숙','1002',3300,'2000-01-01'),
('1006','10','20','김말숫','1002',2800,'2001-01-01')

('1007','20','30','홍영자','1003',3500,'2000-12-01'),
('1008','10','30','이영자','1003',2300,'2002-05-01')

('1009','20','40','김강국','1004',3800,'2001-01-01'),
('1010','10','40','홍정국','1004',2500,'2002-12-01');
INSERT INTO emp VALUES ('1011','10','40','강정국','1004',2500,'2002-12-01');
```



```mariadb
# 1. 직원정보와 연봉 정보를 같이 출력한다.
# 세금은 10%
select empno, titleno, deptno, name, manager, salary, salary*12*0.9 as '연봉',hdate
from emp

# 2. 직원정보 중 2001년 이전에 입사했고 월급이 4000만 원미만인 직원을 조회
select *
from emp
where YEAR(hdate) < 2001
and salary < 4000

# 3. manager가 있는 직원 중 이름에 '자'가 있는 직원 정보를 조회
select *
from emp
where manager != 'NOTNULL'
and name like '%자%'

# 4. 월급이 2000만 미만은 '하', 4000만 미만은 '중', 4000만 이상은 '고'로 출력
select *,
	case
		when salary < 2000 then '하'
		when salary < 4000 then '중'
		when salary >= 4000 then '상'
	end as 'level'
from emp

# 6. 부서 별 대리와 사원 연봉의 평균을 구하시오 단, 평균이 2500 이상인 부서만 출력

select titleno, deptno, avg(salary)
from emp
group by titleno
having titleno = '10' or titleno = '20'
and avg(salary) > 2500

# 7. 2000년 부터 2002년에 입사한 직원들의 월급의 평균을 구하시오

select avg(salary) as '평균 월급'
from emp
where YEAR(hdate) between '2000' and '2002'

# 8. 부서 별 월급의 합의 ranking을 1위부터 조회 하시오

select deptno, avg(salary),
	RANK() OVER (order by avg(salary) desc) AS 'ranking'
from emp
group by deptno

# 9. 서울에서 근무하는 직원들을 조회 하시오

select *
from emp
where titleno = '10'

# 10. 이영자가 속한 부서의 직원들을 조회 하시오

select *
from emp
where deptno = (select deptno from emp where name like '이영자')

# 11. 김강국의 타이틀과 같은 직원들을 조회 하시오

select *
from emp
where titleno = (select titleno from emp where name like '김강국')
```



```mariadb
# 1. 2000년 이후 입사한 사원의 정보를 출력: 사번, 이름, 타이틀, 부서, 지역
select e.empno, e.name, t.titlename, d.deptname, d.deptloc
from emp e
INNER JOIN title t
ON t.titleno = e.titleno
INNER JOIN dept d
ON d.deptno = e.deptno
WHERE YEAR(e.hdate) >= 2000

# 사장에 대한 입력정보가 없기 때문에 INNER JOIN과 OUTER JOIN을 쓴 결과가 같다.
# 그러나 중요한 정보가 소실될 수 있으므로 유의해야 한다.
select e.empno, e.name, t.titlename, d.deptname, d.deptloc
from emp e
LEFT OUTER JOIN title t
ON t.titleno = e.titleno
LEFT OUTER JOIN dept d
ON d.deptno = e.deptno
WHERE YEAR(e.hdate) >= 2000

# 2. 부서 이름별 월급의 평균
select d.deptname, AVG(e.salary) AS avgsal
from emp e
INNER JOIN dept d
ON d.deptno = e.deptno
group by d.deptname
HAVING avgsal > 3000

# 3. 경기 지역의 직원들의 평균 연봉
select d.deptloc, AVG(e.salary)
from emp e
INNER JOIN dept d
ON e.deptno = d.deptno
group by e.deptno
HAVING e.deptno = '20'

# 4. 홍영자 직원과 같은 부서 직원의 근무 월수를 구하시오
select e.name, d.deptname, PERIOD_DIFF(DATE_FORMAT(NOW(),'%Y$m'),DATE_FORMAT(hdate,'%Y%m')) AS '근무월수'
from emp e
INNER JOIN dept d
ON e.deptno = d.deptno
where e.deptno = (select deptno from emp where name like '홍영자')

# 5. 정년이  많은 직원 순으로 정렬하여 순위를 정한다. : 이름, 부서명, 직위, 년수
select e.name, d.deptname, t.titlename, (YEAR(NOW())-YEAR(hdate)) AS 'wyear',
	RANK() OVER (order by wyear desc) AS 'RANK'
from emp e
LEFT OUTER JOIN dept d
ON e.deptno = d.deptno
INNER JOIN title t
ON e.titleno = t.titleno
```



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

