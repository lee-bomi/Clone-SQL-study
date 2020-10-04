FOREIGN KEY
======================
* 자식테이블에서 부모테이블의 특정ROW를 식별하게하는 컬럼  
* "참조무결성"을 지키기 위해 필요  
------------------------
```SQL
ALTER TABLE review ADD CONSTRAINT fk_review_table
  FOREIGN review(customer_id)
  REFERENCES course(id)
    ON DELETE RESTRICT
    ON UPDATE CASCADE
```

* 부모 row가 삭제 or 수정될때 자식row에게 끼치는 영향
1. RESTRICT : 부모나 자식ROW를 삭제를 시도해도 삭제가 안됨
```SQL
Error Code: 1451. Cannot delete or update a parent row: a foreign key constraint fails('course_rating'.'review',CONSTRAINT 'fk_review_')
```
2. CASCADE(연쇄작용) : 부모쪽에서 수정,삭제시 자식ROW에도 수정삭제가 적용됨(모든내용이 남지않고 모두 삭제)
3. SET NULL : 부모쪽에서 수정,삭제시 자식쪽 FOREIGN KEY에 NULL로 표시되고 나머지 정보는 남아있음

※ 각 DELETE/UPDATE에 각자다른 정책을 사용해도됨
