### JOIN
JOIN에 쓰는 옵션  
1. USING : 조인하려는 테이블에 같은이름의 컬럼이있을때(ex) USING(deptno) => 괄호안에는 alias불가  
2. ON(더많이 쓴다)  


```sql
drop table department;
create table department
(
    Dept_id varchar2(10) not null,
    Dept_Name varchar2(25) null,
    Dept_Tel varchar2(12) null
);
desc department;
select * from tab;  -- 테이블명 조회 
-- 제약조건 추가
drop table department;
drop table student;
create table Department
(
    Dept_id varchar2(10) constraint Department_pk primary key,
    Dept_Name varchar2(25),
    Dept_Tel varchar2(12)
    -- constraint Department_pk primary key (Dept_id)
); -- 제약조건(constraint), 제약조건이름 department_pk 
commit;
-- select * from user_constraint where table_name = Department;

insert into department values ('green_id', '컴퓨터학원', '010');
select * from department;

-- 문제) 테이블생성_ 테이블 이름 SG_Scores
-- 칼럼명 student_id 문자형(7글자)
-- 칼럼명 course_id 문자형(5)
-- 칼럼명 Score 숫자형 Number(3)
-- 칼럼명 Grade 문자(2) 
-- 칼럼명 Score_Assigned 날짜(Date)
-- 제약조건(constraint) 제약조건명(SG_Score_pk) primary key
-- (Student_Id, Course_id) => 2개의 칼럼으로 하나의 primary key생성 

create table SG_Scores 
(
    student_id varchar2(7),
    course_id varchar2(5),
    Score number(5),
    Grade varchar(2),
    Score_Assigned date,
    constraint SG_Score_pk primary key (Student_Id, Course_id)
);
commit;
desc SG_Scores;
select * from tab;

insert into SG_Scores values('emma2', 'dex', 101, 'd', '2020-10-05');
commit;
select * from SG_Scores;
-- 서버프로그램에서 mybatis에서 sql구문을 사용해야 합니다. 

create table student (
    dept_id varchar2(10) constraint student_dept_id_fk
        references department, 
    year varchar(1),
    student_id varchar2(7) primary key,
    name varchar2(20) not null,
    id_number varchar2(14) not null,
    address varchar2(40)
);
commit;

create table professor(
    professor_id varchar2(3) primary key,
    name varchar2(20) not null,
    position varchar2(10) not null,
    dept_id constraint professor_fk references department(dept_id),
    telephone varchar2(12)  -- department(학과)
);
commit;
desc professor;

create table course (
    course_id varchar2(5) primary key,
    title varchar2(20),
    c_number number(1) not null,
    professor_id varchar2(3) constraint student_fk references professor (professor_id)
);
commit;
desc course;
select * from user_constraints where table_name = 'COURSE';    -- 대문자로해야함
        -- 테이블이름으로 한정지음
        -- where절이 없으면 모든 테이블에 대한 constraint이 출력됨
--테이블에 해당하는 제약조건 조회

drop table course;  
drop table professor;  -- 참조된 테이블은 바로 삭제가 안됨(먼저 자식테이블 삭제하고 부모테이블 삭제하기)
drop table sg_scores purge;  -- 휴지통에서도 삭제됨
select dept_id, dept_name, dept_tel from department; -- 조회 명령
select distinct dept_id as "소속학과" from professor order by '소속학과'; -- alias(rename) / 중복제거, unique = distinct 중복제거한 데이터 검색 
-- order by 1 : 칼럼의 몇번째 
desc department;
select * from department order by 3;  -- 3은 dept_tel과 동일 
select * from department order by 3 desc;  -- dept tel을 내림차순

-- 문제) dept_id가 컴공인 사람(컴공과 교수님)을 교수님테이블에서 가져와 교수님id, 이름, 직책,학과,id를 출력하세요
select professor_id, name, position, dept_id  from professor order by position;
select professor_id, name, position, dept_id,
    case
        when position ='총장' then 1
        when position ='교수' then 2
        when position ='부교수' then 3
        when position ='조교수' then 4
        else 5 end    as 직위
    from professor order by 직위;
-- 문제1) course 테이블로 부터 추가 수강료(course_fees) 가 30000원 이상인 과목을 출력
select course_id, title, course_fees from course where course_fees >= 30000 order by course_fees desc;

-- 문제2) student테이블에서 컴공과 2학년(year) 학생의 학과, 학년, 성명 출력
select dept_id, year, name from student where dept_id = '컴공' and year =2; 
select dept_id, year, name from student where (dept_id, year) in(('컴공', 2));  -- 위와같은표현(집합사용)
select dept_id, year, name from student where (dept_id, year) in(('컴공', 2),('컴공', 3)); -- 이렇게도 가능

select '이' || '재오' || '그린' || '강사입니다' || position 
    from professor;  -- mysql에서의 결합(concat)을 ||으로
select dept_id, year, student_id, name from student where name like '이%';

-- 문제3)student 테이블로부터 중간 글자가 정인 학생 출력
select name from student where name in '정';

select dept_id, year student_id, name from student
    where name not like '이%' and name not like '김%';
    
-- 문제4) professor테이블에서 학과코드(dept_id)가 컴공, 정통학과에 재직중인 교수의 명단을 학과코드순으로 출력
                              --or또는 in사용
select dept_id, name from professor where dept_id in ('컴공', '정통학과') order by dept_id asc;
                              
-- 문제5) professor테이블에서 학과코드(dept_id)가 컴공, 정통학과가 아닌 교수의 명단을 학과코드순으로 출력,
        -- where 칼럼 not in (집합)
select dept_id, name from professor where dept_id not in ('컴공', '정통학과') order by dept_id asc;

-- 문제6) sg_scores 테이블에서 성적(score)이 90에서 94점까지 성적을 성적 내림차순으로 출력, between a and b
select student_id, score from sg_scores where score between 90 and 94;

-- 문제7) sg_scores 테이블에서 성적(score)이 80점부터 100점까지 제외한 성적을 성적 내림차순으로 출력, not between a and b
select score from sg_scores where score not between 80 and 100 order by score desc;
select * from course where course_fees is null order by title;  -- course_fees가 null인값 정렬
select lower('hello') , upper('welcome') from dual; -- 문자열 소문자, 대문자 -- lower('HEL UPPER('Welcome'))
select name, substr(name,1,3), email, instr(email,'@') from professor order by 1; 
-- instr은 특정문자의 위치 반환
-- substr(문자열시작, 잘라올 데이터 갯수), 0부터 index 시작함
select ltrim ('xyxyxHello', 'xy') from dual; -- ,x와 y왼쪽 문자열 제거(x,y가 잘려서도 각각 자름)
select rtrim ('oracle serverkkkk', 'k') from dual; -- 오늘쪽 x문자열 제거 
select sysdate, current_date, current_timestamp from dual;
select dept_id, year, name, i_date, current_date "기준일",
    trunc(MONTHS_BETWEEN(CURRENT_DATE, i_date)) "재적일수"
        from student
            where dept_id = '컴공' order by 4;
-- mysql cast사용
select to_char(current_timestamp, 'yyyy-mm-dd hh24:mi:ss ff3') from dual; --문자열로 날짜를 형변환
--문제) SG_Score 테이블에서 성적(Score) 이 98점 이상자에 대하여 성적취득일자를 'yyyy/mm/dd'형식으로 문자형으로 변환하세요
select score,to_char(score,'s999'), to_char(score, 'b999.9'), to_char(score,'099.99') from sg_scores 
    where score >= 98 order by 1 desc; -- s:부호(sign),b:공백으로 채움
select course_fees, to_char(course_fees, 'L99,999') "국가별 화폐",
    to_char(course_fees, 'C999G999') "ISO 화폐"
        FROM course where course_fees = 30000;
select * from course where course_fees = 30000;
select to_char(12345678, 'L999,999,999') 지역 FROM DUAL;

select dept_id, name, id_number, 
    decode (substr(id_number, 8, 1), '1', '남', '2', '여', '3', '남', '4', '여') 성별 
        from student
            where dept_id = '컴공' order by 성별;
select student_id, course_id, score,
    case when score between 90 and 100 then 'A'
        when score between 80 and 90 then 'B'
        when score between 70 and 79 then 'C'
        when score between 60 and 69 then 'D' 
        else 'F' 
    end "등급"
    from sg_scores
    where student_id = 'C1601' order by 2;
    
select count(*), count(email) from student ; -- student null은 3개 
-- student의 email이 null인 데이터 출력 
select email, dept_id from student where email is null;
-- SCORE의 칼럼의 데이터의 총합과 평균을 출력함
select sum(score), avg(score) from sg_scores where course_id ='L1031';
select dept_id, count(*) from professor 
    group by dept_id; -- 학과(dept_id) 별 수량출력
select unique dept_id from professor;
select dept_id from professor 
    group by dept_id 
        having count(*) =1; -- 학과가 하나인것들만 골라내라
        -- 집계(aggregation) 함수 -> count, sum, avg, stddev, ..
        -- 는 where절에 사용불가 
select dept_id from professor
    where count(*) =1 -- 학과가 하나인것들만 골라내라
    group by dept_id;
    -- 집계(aggregation)함수 -> count, sum, avg, stddev,..
    -- 는 where절에 사용불가 
    
-- 조인
select p.professor_id, name, position, title c_number
    from professor p, course c
        order by 1; -- professor테이블과 course 테이블을 조인함
            --cartisian join(곱(product)) 
-- professor의 record의 갯수가 30개이고 course의 레코드의 갯수가 10개이면 300개의 데이터가 생성됨
select count(professor_id) from professor; -- 10개
select count(course_id) from course; -- 12개 

-- professor 테이블과 course테이블을 이용하여 교수가 최소한 한 과목 이상을 담당하고 있는 교수의
-- [교수번호, 교수명, 직위, 과목명, 학점수]를 교수 번호순으로 출력

-- 비정상적인 조인
select p.professor_id, name, position, title, c_number
    from professor p , course c
        where p.professor_id = c.professor_id
            order by 1; -- 아마도 중복된 데이터만 골라내지 않았을까?
select count(professor_id) from course; -- 8개
select count(professor_id) from professor; -- 10개

-- 정상적인 조인(테이블조인 => using()을 사용하던지 on(   =  )을 사용하던지 선택
-- inner, right, left, 카티션(cross)join은 모든 가지수의 조인
-- self join은 자기 자신의 테이블에 서로다른 칼럼을 기준으로 join 
select p.professor_id, name, position, title, c_number
    from professor p left outer join course c  
        on p.professor_id = c.professor_id 
             order by 1;  -- professor 테이블 기준으로 조인 

-- self jon
select t1.professor_id, t1.dept_id, t1.duty,
    t1.name || ' ' || t1.position "교수명",
    t1.name || ' ' || t2.duty "관리자명"
    from professor t1, professor t2
        where t1.mgr = t2.professor_id;
            --order by 2,3;

select * from professor; -- 관리자코드
-- select professor_id from professor; -- 직무 
--널 제외, email+duty
select substr(email, 0, instr(email,'@')) from professor 
    where duty is not null;
    
    
-- 10/06
-- 집합 연산자(intersect) 교집합
select professor_id, name from professor
    intersect
    select professor_id, name from course inner join professor using(professor_id)
    order by 1;  -- intersect는 교집합 6개, union(합집합) 10개, minus(합집합)

desc SG_Scores;
select unique student_id, course_id from sg_scores;
select student_id, name
    from student S 
        where not exists (select * from SG_Scores SG 
                            where SG.Student_id = S.Student_id); 
select unique student_id, name from student; -- 서브쿼리(집합)에 존재(exist)하지 않은 데이터만 출력


select UNIQUE sg.student_id, name
    from sg_scores sg inner join student s 
        ON (sg.student_id = s.student_id)
            where sg.student_id = 'C1601';

-- 집합연산 : 같은종류의 테이블일때만 가능하다!
select count(*) from student;
select count(*) from sg_scores;
select count(*) from student  
    minus
        select count(*) from sg_scores;
-- 테이블 원소의 개수 - 테이블 원소의 개수
select count(*) from sg_scores 
    minus
        select count(*) from student;
        
-- 두테이블의 교집합 원소의 개수 
select count(*) from 
    (select student_id from sg_scores 
        intersect
            select student_id from student);

-- 갯수출력하기(조인 변경하여 비교해보기)
select count(s.name), count(sg.student_id) 
    from sg_scores sg right outer join student s 
        on sg.student_id = s.student_id;
        
-- 아래 2개의 구문은 같은결과를 나타낸다
select student_id, name
        from student s
            where not exists (select * from sg_scores sg
                where sg.student_id = s.student_id); 

select student_id, name from student 
minus
select student_id, name from sg_scores inner join student using(student_id);

-- 문제
-- sg_scores테이블의 'L1031' 'SQL응용'과목의 행들을 출력하고 L1031과목의 평균점수보다 높은 점수를 출력하세요
    --1) 평균점수구하기
    select avg(score) from sg_scores where course_id = 'L1031';
    --2) 이것을 subquery로 사용하기
    select student_id, course_id, score 
        from sg_scores
            where course_id='L1031'
                and score > (select avg(score) from sg_scores where course_id = 'L1031');
    
-- 문제
-- sg_scores테이블의 'L1031'='SQL응용'과목에서 최고점을 받은 학생들의 학번, 과목코드, 성적, 성적취득일자를 출력(join사용x)
select student_id, course_id, score, score_assigned 
    from sg_scores 
        where course_id = 'L1031'
            and score >= (select max(score) from sg_scores where course_id = 'L1031');
            
-- 다중행 서브쿼리 : SG_Scores 테이블에서의 년월별 최고점을 받은 학생들의 학번, 과목코드, 성적, 성적 취득년월일출력
select student_id, course_id, score, to_char(score_assigned, 'YY/MM') "취득년월"
    from sg_scores
        where (to_char(score_assigned, 'YY/MM') , SCORE) 
        IN (select to_char(score_assigned, 'YY/MM') , MAX(Score)
            from sg_scores
                group by to_char(score_assigned, 'YY/MM'))
                    order by 4;
-- 위의구문중 서브쿼리만 떼다가 확인(mysql의 covert를 오라클에서는 to_char)
select to_char(score_assigned, 'YY/MM') , MAX(Score)
     from sg_scores
        group by to_char(score_assigned, 'YY/MM');

-- 문제
-- sg_scores테이블의 'L1031'('SQL응용')과목에서 성적이 최하점수보다 높은학생에 대하여 
--학번, 과목코드, 성적, 성적취득일자를 출력
select student_id, course_id, score, score_assigned 
    from sg_scores
        where course_id = 'L1031'
            AND Score > any(select score from sg_scores where course_id='L1031');
-- any 집합의 조건중 하나라도 만족하면..
-- 등가(=) 비등가(< >) 는 비교값이 하나의 행(레코드) 이어야 하므로, any제거하면 복수개의 record가 나와서 에러


-- 인라인 뷰
-- select 문의 from 절에 기술하는 서브쿼리 
-- 인라인 뷰에 별칭을 사용하면 인라인뷰에서 반환된 값을 메인쿼리에서도 사용가능 
-- TOP-N 쿼리 구문 
-- SG_Score 테이블을 성적과 학점을 내림차순으로출력하고, 1페이지에
-- 출력할 성적 상위자 5명의 학번 성명, 과목명, 학점수, 성적, 등급을 출력하시오
SELECT student_id, name, title, c_number, score, grade
    FROM sg_scores INNER JOIN course USING(course_id)
        INNER JOIN student USING(student_id)
    WHERE score IS NOT NULL
        order by 5 desc, 4 desc; 
SELECT ROWNUM "순위", a.*
    FROM (SELECT student_id, name, title, c_number, score, grade
        FROM sg_scores INNER JOIN course USING(course_id)
            INNER JOIN student USING(student_id)
        WHERE score IS NOT NULL
            order by 5 desc, 4 desc) a
        WHERE ROWNUM <= 5; -- 인라인 뷰를  활용한 상위 5개 출력(부등호는 바꿀수없다, 개수제한둬서 번호붙이는거라서), ROWNUM은 오라클에서 관리하는 행 번호
--SG_Score 테이블에서 3페이지에 출력할 성적상위자 5명(11-15위)에 대하여 학번, 서명, 과목명, 학점수, 성적등급을 출력 
SELECT *
    FROM (SELECT ROWNUM num, a.*
        FROM (SELECT student_id, name, title, c_number, score, grade
            FROM sg_scores INNER JOIN course USING(course_id)
                            INNER JOIN student USING(student_id)
            WHERE score IS NOT NULL
                order by 5 desc, 4 desc) a)
        WHERE NUM BETWEEN 11 AND 15;  -- 5개 데이터가 1페이지 표시될경우 
-- 서브쿼리를 이용한 빈테이블 생성
CREATE TABLE course_temp
    AS SELECT * FROM course WHERE 1=2;
DESC course_temp;
SELECT * FROM course_temp; -- 빈테이블 생성, 기존 칼럼을 유지하면서 


-- 시퀀스
-- 시퀀스는 데이터베이스 객체로서 시퀀스 생성시 설정규칙에 따라 정수를 반환한다.
-- 시퀀스는 기본키값을 자동적으로 생성하거나 난수(랜덤) 생성에 사용될 수 있음.
DROP SEQUENCE dept_seq;
CREATE SEQUENCE dept_seq INCREMENT BY 1;  -- dept_seq 시퀀스를 이용하여 DEPARTMENT테이블의 모든 행에 순서번호 출력 
SELECT dept_seq.NEXTVAL ,dept_id, dept_name, dept_tel FROM department;
DESC computer_student;

--문제
-- 테이블 생성_computer_student
CREATE TABLE computer_student(
    student_id varchar2(6),
    dept_id varchar2(10),
    year varchar2(10),
    name varchar2(10),
    id_number varchar2(20)
);
-- 학번(c_number)은 시퀀스 이용하여 데이터 입력
CREATE SEQUENCE st_seq START WITH 1501 INCREMENT BY 1 MAXVALUE 1700;  -- 시퀀스생성하면 데이터입력시 자동으로 nextval이 내부에서 호출되면서 자동으로 숫자가 증가
COMMIT;
--INSERT INTO computer_student(student_id, dept_id, year, name, id_number) VALUES ('C1501', '컴공', '2', '홍길동', '990101-1******');
--INSERT INTO computer_student(student_id, dept_id, year, name, id_number) VALUES ('C1502', '컴공', '2', '최지우', '990203-2******');
--INSERT INTO computer_student(student_id, dept_id, year, name, id_number) VALUES ('C1503', '컴공', '2', '이승호', '981122-1******');

INSERT INTO computer_student(student_id, dept_id, year, name, id_number)
    VALUES (concat('C',LTRIM(TO_CHAR(ST_SEQ.NEXTVAL,'9999'))),
        '&학과', '&학년', '&성명', '&주민번호');
SELECT st_seq.NEXTVAL FROM computer_student;
SELECT * FROM computer_student;


-- 인덱스 (최적화/optimization) 
--explain plan문법
explain plan 
    for 
        select * from course where course_id = 'L1031';
SELECT * FROM TABLE(dbms_xplan.display); -- 실험결과 보기 

explain plan
    for 
        select professor_id, name, position, c_number
            from professor inner join course using(professor_id)
            order by 1;
SELECT * FROM TABLE(dbms_xplan.display);-- 실험결과 보기 

-- 옵티마이즈 힌트 (hint)
SELECT /*+ first_rows(3) */ *
    from course
        where rownum <=3; -- /*+          */ 이 안에 어떻게 최적화할지 정보를 줌
    

SELECT /*+ ALL_ROW (3) */ *
    from course
        where course_id <=3; -- /*+          */ 이 안에 어떻게 최적화할지 정보를 줌
        


-- PL/SQL (Procedual Language/SQL) --> 한줄한줄이아닌 블록단위로 실행됨.
-- 오라클 데이터베이스 실행되는 절차적인 데이터베이스 프로그래밍 언어
-- 표전SQL과 3세대 언어의 강력한 일부기능을 포함하나 SQL확장언어
-- 프로그램 단위를 블록이라 부르며 어플리케이션 로직을 작성
-- SQL문은 명령문 단위로 입력하여 실행하는데 PL/SQL은 여러개의 SQL문과 PL/SQL 명령문을 프로그램 단위로 실행함
-- PL/SQL 블록을 사용하는 하나의 장점은 네트워크 트래픽의 감소를 들수있음
-- - 업무규칙이나 복잡하나 로직을 캡슐화하여 모듈화 및 추상화함
-- - 독립적인 플랫폼

-- => 기본구조는 아래와 같다
-- DECLARE -> 선언부
-- BEGIN 실행부(필수)
-- <여기에 코드작성>
-- END(필수)

SET SERVEROUTPUT ON --시스템에 서버의 출력을 활성화 dbms_output.put_lineI() 내장 프로시저 활성화
DECLARE 
    v_avg NUMBER(3) := 0;
    v_student_id VARCHAR2(5) := 'C1701';
BEGIN
    dbms_output.enable; 
    SELECT AVG(SCORE)
        INTO v_avg
        FROM SG_Scores
        WHERE student_id = v_student_id;
        dbms_output.putline(v_student_id || '의 평균 점수는 [' || v_avg || ']점 입니다.');
END;
/

-- 변수나 상수 선언시 SQL객체(뷰/테이블/시퀀스/프로시저)명과 동일한 규칙으로 정의
-- 변수와 상수는 반드시 데이터타입과 일치하는 값을 기술
-- 한줄에 한개의 변수나 상수를 정의함.
-- 초기값이 NULL이나 0인경우 생략가능

-- 테이블을 참고하여 변수선언 
-- %TYPE, %ROWTYPE 데이터 타입은 기존 테이블에 정의된 데이터 타입과 크기를 참고하여 변수선언 
-- %TYPE은 단일 칼럼을 참고
-- %ROWTYPE은 복수개의 칼럼참고
-- %TYPE은 테이블의 칼럼에 대한 데이터 타입이나 크기가 변경되어도 수정할 필요가없기때문에 테이블 칼럼에 관련된 변수선언에 유용함.


--DECLARE
--    v_student_id SG_Scores.student_id%Type := 'C1701';
--    v_avg        SG_Scores.Score%type :=0;
---v_grade      SG_Scores.Grade%TYPE;                  -- NULL이나 초기값일때 :=0(초기값 문법)안씀
--    v_cnt        NUMBER(2) := 0;
    -- 여기까지 변수 선언 및 초기화
DESC sg_scores;
DECLARE
    v            SG_Scores%ROWTYPE;
    v_cnt        NUMBER(2) :=0;
    
BEGIN 
    v.student_id     := 'C1701';
    SELECT COUNT(*), AVG(score)
        INTO v_cnt, v.Score
        FROM SG_Scores
        WHERE Student_id = v.student_id;
    IF v.score>=90
        THEN v.grade    :=  'A';
        ELSIF V.score  >= 80
            THEN v.grade  := 'B';
            ELSIF v.score >= 70
                THEN v.grade  :='C';
                ELSIF v.grade >= 60
                    THEN v.grade  :='D';
                    ELSE v.grade :='E';
    END IF;
    dbms_output.put_line(v.student_id || '의 과목수는 [' || v_cnt || ']이고 평균 점수는 [' || v.score || ']점 [' ||v.grade || '] 등급입니다.');
END;
/
-- ABC가아닌 수우미양가로 하려면 아래의 코드가 필요하다
--ALTER TABLE sg_scores
--    MODIFY/CHANGE columns grade VARCHAR2(5) 
--ALTER TABLE sg_scores
--    DROP COLUMN grade;
--ALTER TABLE sg_scores
--    add column grade VARCHAR2(5)




-- 반복문
CREATE TABLE Temp (
    col1 number(3),
    col2 DATE
);
DECLARE 
    MAX_No CONSTANT POSITIVE := 10;  --상수선언, 변경불가
    i NATURAL :=0;
BEGIN 
    LOOP
        i:= i+1;
        EXIT WHEN i>MAX_NO;
        INSERT INTO Temp Values (i, sysdate);
    END LOOP;
END;
/
SELECT * FROM temp;
-- 변수데이터 타입 POSITIVE => 정수중 1~214748364
-- 변수데이터 타입 NATURAL => 정수중 1~214748364
-- 데이터를 짝수인 데이터만 테이블에 추가


-- for문
declare
    MAX_NO CONSTANT POSITIVE := 990;
    i natural :=15;
begin
    for i in 1..MAX_No Loop
        if
            i-3*round(i/3) =0 then 
            insert into temp values (i, sysdate);
        end if;
    end loop;
end;
/
select*from temp;



-- 서브 프로그램
CREATE TABLE patient(
    patient_id VARCHAR2(6) NOT NULL, -- 환자 ID
    body_temp_deg_c NUMBER(4,1),  -- 체온, 화씨
    body_temp_deg_f NUMBER(4,1),   -- 체온, 섭씨
    insurance VARCHAR2(1)  --환자테이블 생성
);
DESC patient;
-- PATIENT 테이블에서 환자번호, 환자의 체온(섭씨), 환자의 체온을 화씨로 계산하여 저장하는 블록을 작성
DECLARE
    v_patient_id patient.patinet_id%type;
    v_temp_deg_f number(4,1) :=0;
    v_temp_deg_c number(4,1) :=0;
BEGIN
    v_patient_id := 'YN0001';
    v_temp_deg_c := 40.0;
    v_temp_deg_f := (9.0/5.0)*v_temp_deg_C+32.0;
    INSERT INTO patient (patient_id, body_temp_deg_c, body_temp_deg_f)
        VALUES(v_patient_id, v_temp_deg_c, v_temp_deg_f);
    v_patient_id := 'YN0002';
    v_temp_deg_c := 41.0;
    v_temp_deg_f := (9.0/5.0)*v_temp_deg_C+32.0;
    INSERT INTO patient (patient_id, body_temp_deg_c, body_temp_deg_f)
        VALUES(v_patient_id, v_temp_deg_c, v_temp_deg_f);
END;
/
SELECT * FROM patient;


-- 서브 프로그램 
DECLARE
    v_patient_id patient.patinet_id%type;
    v_temp_deg_f number(4,1) :=0;
    v_temp_deg_c number(4,1) :=0;
    -- 프로시저 서브프로그램
    PROCEDURE body_temp_change_f (f_patient_id_ VARCHAR2,
        f_temp_deg_c number) IS
        f_temp_deg_f number(4,1) :=0; 
BEGIN
    v_patient_id := 'YN0001';
    v_temp_deg_c := 40.0;
    v_temp_deg_f := (9.0/5.0)*v_temp_deg_C+32.0;
    INSERT INTO patient (patient_id, body_temp_deg_c, body_temp_deg_f)
        VALUES(v_patient_id, v_temp_deg_c, v_temp_deg_f);
    v_patient_id := 'YN0002';
    v_temp_deg_c := 41.0;
    v_temp_deg_f := (9.0/5.0)*v_temp_deg_C+32.0;
    INSERT INTO patient (patient_id, body_temp_deg_c, body_temp_deg_f)
        VALUES(v_patient_id, v_temp_deg_c, v_temp_deg_f);
END;
/
SELECT * FROM patient;
```
