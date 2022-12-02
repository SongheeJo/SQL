* 문제 : https://leetcode.com/problems/department-highest-salary/

```SQL

/* 리트코드의 버전이 낮아서 윈도우 함수 지원 X -> MS SQL Server로 놓고 문제풀기

<문제 이해>
부서별 / 중복 허용 / top / 출력
<idea>
1) 부서별 -> PARTITION BY Department.name
2) 중복허용 top = Rank()나 Dense_Rank() ... AS TOP (TOP 칼럼 생성)
3) TOP = 1 자르기 -> WHERE TOP = 1

<구현> */
SELECT TOP
     , e.salary AS salary
     , e.name AS name
  FROM (SELECT e.name
             , e.salary
             , RANK() OVER (PARTITION BY Department.name) AS TOP
          FROM Employee e
         INNER JOIN Department d
                 ON e.departmentId = d.id) AS preprocessed
 WHERE TOP = 1


-- [42000] [Microsoft][ODBC Driver 17 for SQL Server][SQL Server]Incorrect syntax near ','. (102) (SQLExecDirectW)
-- 어디 ,가 오류인지 확인 필요


-- 풀이 2
-- DENSE_RANK()로 풀기




-- 풀이 3
-- 서브쿼리 (MySQL에서 풀기)

/* <문제 이해>
1) 부서별 -> GROUP BY
2) TOP -> MAX
3) 결괏값 not unique => WHERE절 + IN

<idea>
WHERE절 서브쿼리)
WHERE   >     결괏값 로우 1개(WHERE절에 비교연산자 사용시)
			  IN    결괏값 로우 N개, 칼럼 1개(WHERE절에 IN, NOT IN 사용시)

<구현>
*/

SELECT d.name AS Department
     , e.name AS Employee
     , e.salary AS Salary
  FROM Employee e
 INNER JOIN Department d ON e.departmentId = d.id
 WHERE salary IN (SELECT MAX(salary) 
                   FROM Employee
                  GROUP BY departmentId)


-- 풀이 4
-- MAX()로 풀기

SELECT Department
     , Employee
     , Salary
  FROM (SELECT e.id
             , e.name AS Employee
             , e.salary AS Salary
             , d.name AS Department
             , MAX(salary) OVER (PARTITION BY departmentId) max
          FROM Employee e
               INNER JOIN Department d ON e.departmentId = d.id
        ) ms
 WHERE ms.salary = ms.max



```
