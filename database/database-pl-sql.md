# PL/SQL 

- Procedural Language/Structured Query Language
- 데이터베이스 응용 프로그램을 작성하는 데 사용하는 오라클의 SQL 전용 언어
- 프로시저, 트리거, 사용자 정의 함수



1. 프로시저

   - 삽입 작업을 하는 프로시저

     ```plsql
     CREATE OR REPLACE PROCEDURE InsertEmployee(
     	employeeId IN NUMBER,
     	lastName IN VARCHAR2,
       	email IN VARCHAR2,
       	hireDate IN DATE,
       	jobId IN VARCHAR2)
     AS
     BEGIN
       INSERT INTO EMPLOYEES(EMPLOYEE_ID, LAST_NAME, EMAIL, HIRE_DATE, JOB_ID) 				VALUES(employeeId, lastName, email, hireDate, jobId);
     END;
     ```

     ```plsql
     /* 테스트 */
     EXEC INSERTEMPLOYEE(EMPlOYEES_SEQ.NEXTVAL, 'Rora', 'rora@test.com', sysdate, 'IT_PROG');
     SELECT * FROM EMPLOYEES where LAST_NAME = 'Rora';
     ```

     ​


   - 제어문을 사용하는 프로시저

     ```plsql
     CREATE OR REPLACE PROCEDURE UpdateEmployee(
     	employeeId IN NUMBER,
     	lastName IN VARCHAR2,
       email IN VARCHAR2,
       jobId IN VARCHAR2)
     AS
       myCount NUMBER;
     BEGIN
       SELECT COUNT(*) INTO myCount FROM EMPLOYEES WHERE EMPLOYEE_ID = employeeId;
       IF myCount != 0 THEN
         UPDATE EMPLOYEES
         SET LAST_NAME = lastName, EMAIL = email, JOB_ID = jobId
         WHERE EMPLOYEE_ID = employeeId;  
       END IF;
     END;
     ```

     ```plsql
     /* 테스트 */
     SELECT * FROM EMPLOYEES where EMPLOYEE_ID = 101;    -- JOB_ID = AD_VP
     EXEC UPDATEEMPLOYEE(101, 'Jane', 'JANE1','ST_MAN');
     SELECT * FROM EMPLOYEES where EMPLOYEE_ID = 101;    -- JOB_ID = ST_MAN
     ```

     ​

   - 결과를 반환하는 프로시저

     ```plsql
     CREATE OR REPLACE PROCEDURE AverageSalary(
       AverageVal OUT NUMBER)
     AS
     BEGIN
       SELECT AVG(SALARY) INTO AverageVal FROM EMPLOYEES WHERE SALARY IS NOT NULL;
     END;
     ```

     ```plsql
     /* 테스트 */
     SET SERVEROUTPUT ON;
     DECLARE
       AverageVal NUMBER;
     BEGIN
       AVERAGESALARY(AverageVal);
       DBMS_OUTPUT.PUT_LINE('임금 평균: ' || AverageVal);
     END;		-- 임금 평균: 6461.831775700934579439252336448598130841
     ```

     ​

   - 커서를 사용하는 프로시저

     ```plsql
     CREATE OR REPLACE PROCEDURE TotalCommission
     AS
       myTotalCommission NUMBER;
       mySalary NUMBER;
       myCommissionPct NUMBER;
       CURSOR CommissionCursor IS SELECT SALARY, COMMISSION_PCT FROM EMPLOYEES;
     BEGIN  
       myTotalCommission := 0.0;
       OPEN CommissionCursor;
       LOOP
         FETCH CommissionCursor INTO mySalary, myCommissionPct;
         EXIT WHEN CommissionCursor%NOTFOUND;
         IF myCommissionPct IS NOT NULL AND myCommissionPct > 0 THEN     
           myTotalCommission := myTotalCommission + (mySalary * myCommissionPct);
         END IF;
       END LOOP;
       CLOSE CommissionCursor;
       DBMS_OUTPUT.PUT_LINE('Total Commission: ' || myTotalCommission);
     END;
     ```

     ```plsql
     /* 테스트 */
     SET SERVEROUTPUT ON;
     EXEC TotalCommission;			-- Total Commission: 73690
     ```

     ​

2. 트리거

   : 데이터의 변경(INSERT, DELETE, UPDATE)문이 실행될 때 자동으로 같이 실행

   : 데이터의 변경문이 처리되는 세가지 시점, 실행전(BEFORE), 대신하여(INSTEAD OF), 실행 후(AFTER)에 동작

   ```plsql
   CREATE OR REPLACE TRIGGER HistoryEmployee
     AFTER UPDATE ON EMPLOYEES FOR EACH ROW
   BEGIN
     INSERT INTO SALARY_HISTORY(EMPLOYEE_ID, SALARY_BEFORE, SALARY_AFTER) 								VALUES(:old.EMPLOYEE_ID, :old.SALARY, :new.SALARY);
   END;
   ```

   ```plsql
   /* 테스트 */
   UPDATE EMPLOYEES
   SET SALARY = 20000
   WHERE EMPLOYEE_ID = 102;
   commit;
   SELECT * FROM SALARY_HISTORY WHERE EMPLOYEE_ID = 102;
   ```

   ​

3. 사용자 정의 함수

   : 입력된 값을 가공하여 결과 값으로 반환

   : EXEC 명령에 의해 실행되는 프로시저와 다르게 SELECT문이나 프로시저 내에서 호출되어 SQL문이나 프로시저에 그 값을 제공하는 용도로 사용

   ```plsql
   CREATE OR REPLACE FUNCTION func_Commission(
     Salary NUMBER,
     CommissionPct NUMBER) RETURN INT
   IS
     myCommission NUMBER;
   BEGIN
     IF CommissionPct IS NOT NULL AND CommissionPct > 0 THEN myCommission := Salary * CommissionPct;
     ELSE myCommission := 0;
     END IF;
     RETURN myCommission;
   END;
   ```

   ```plsql
   /* 테스트 */
   SELECT EMPLOYEE_ID, FIRST_NAME AS NAME, SALARY, COMMISSION_PCT, 
   	FUNC_COMMISSION(SALARY, COMMISSION_PCT) AS COMMISSION
   FROM EMPLOYEES;
   ```

   ​

4.  PL/SQL 문법 요약

   | 구분      | 명령어                                      |
   | :------ | ---------------------------------------- |
   | 데이터 정의어 | CREATE TABLE, CREATE PROCEDURE, CREATE FUNCTION, CREATE TRIGGER, ALTER, DROP |
   | 데이터 조작어 | SELECT, INSERT, DELETE, UPDATE           |
   | 데이터 타입  | NUMBER(n), VARCHAR2(n), DATE             |
   | 변수      | DECLARE문으로 선언, 치환(:=사용)                  |
   | 연산자     | 산술연산자, 비교연산자, 문자열연산자, 논리연산자              |
   | 주석      | --, /* */                                |
   | 내장 함수   | 숫자함수, 집계함수, 날짜함수, 문자함수                   |
   | 제어문     | BEGIN-END, IF-THEN-ELSE, FOR FOOP-END FOOP, (WHILE LOOP-END LOOP, EXIT) |
   | 데이터 제어어 | GRANT, REVOKE                            |

   ​