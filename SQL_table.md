TABLE다루기
================
1. 테이블 이름수정
```SQL
RENAME TABLE apple TO banana
```
2. 테이블 복사하기
새 테이블만들어서 다른테이블의 정보 갖고오기
```SQL
CREATE TABLE apple AS SELECT * FROM banana
```
3. 테이블 컬럼구조만 복사
```SQL
CREATE TABLE copy_of_undergraduate LIKE undergraduate;
```
4. 특정컬럼만 갖고올때
* 서브쿼리문이 SELECT뿐아니라 다른구문에도 쓰일수있음을 알기!
```SQL
CREATE TABLE copy_of_undergraduate SELECT * FROM undergraduate WHERE id=101;
```
5. 테이블의 모든 ROW삭제(컬럼정보만 남길때)
TRUNCATE : 잘라내기
```SQL
TRUNCATE copy_of_undergraduate;
```


