## 정규화



1. 이상현상

   | 학생이름 | 학생번호 | 학과   | 주소       | 강좌이름   | 강의실     |
   | ---- | ---- | ---- | -------- | ------ | ------- |
   | 박지성  | 501  | 컴퓨터과 | 영국 맨체스터  | 데이터베이스 | 공학관 110 |
   | 김연아  | 401  | 체육학과 | 대한민국 서울  | 데이터베이스 | 공학과 110 |
   | 장미란  | 402  | 체육학과 | 대한민국 강원도 | 스포츠경영학 | 체육과 103 |
   | 추신수  | 502  | 컴퓨터과 | 미국 클리블랜드 | 자료구조   | 공학관 111 |
   | 박지성  | 501  | 컴퓨터과 | 영국 맨체스터  | 자료구조   | 공학관 111 |

   | INSERT |      |      |         |      |      |
   | ------ | ---- | ---- | ------- | ---- | ---- |
   | 403    | 박세리  | 체육학과 | 대한민국 대전 | NULL | NULL |

   ​

   - 삭제이상

     - 연쇄삭제(triggered deletion) 문제 발생

       : 투플 삭제 시 같이 저장된 다른 정보까지 연쇄적으로 삭제되는 현상

       > ex) 장미란 학생이 스포츠경영학 과목을 수강 취소하여 3번째 투플을 삭제하면 장미란 학생의 학생 정보 또한 연쇄적으로 삭제

     ​

   - 삽입이상

     - NULL 값 문제 발생

       : 투플 삽입 시 특정 속성에 해당하는 값이 없어 NULL 값을 입력해야 하는 현상

       > ex) 박세리의 입학으로 학생 정보 삽입 시 아직 수강신청은 하지 않은 상태일 경우, (강좌이름, 강의실) 속성에는 NULL값 입력
       >
       > - 사칙연산 결과 오류 발생
       > - SELECT COUNT(*) FROM 학생수강 != SELECT COUNT(강좌이름) FROM 학생수강 

     ​

   - 수정이상

     - 불일치(inconsistency) 문제 발생

       : 투플 수정 시 중복된 데이터의 일부만 수정되어 데이터의 불일치 문제가 일어나는 현상

       > ex) 5번째 투플의 박지성 주소 변경 시 1번째 투플의 주소는 변경되지 않기 때문에 데이터의 일관성을 유지하지 못함

   ​

2. 함수 종속성 

   - 릴레이션 R과 R에 속하는 속성의 집합 X, Y가 있을 때, X 각각의 값이 Y의 값 한 개와 대응이 될 때 'X는 Y를 함수적으로 결정한다'하고 하고 ' X -> Y'로 표기

     (ex. 어떤 속성 A의 값을 알면 다른 속성 B의 값이 유일하게 정해지는 의존 관계)

     ​

   - X를 결정자(determinant), Y를 종속 속성(dependent attribute)

     ```java
     /* 종속 */
     학생번호 -> 학생이름
     학생번호 -> 주소
     강좌이름 -> 강의실
     학과 -> 학과사무실

     /* 종속X */
     학생이름 -> 강좌이름
     학과 -> 학생번호

     /* 종속하는 것처럼 보이지만 그렇지 않은 것 */
     학생이름 -> 학과
     ```

     ​

   - 함수 종속성 규칙

     - 부분집합(Subset) 규칙  : if Y ⊆ X, then X -> Y

     - 증가(Augmentation) 규칙 : if X -> Y, then XY -> YZ

     - 이행(Transitivity) 규칙 : if X -> Y and Y -> Z, then X -> Z

     - 결합(Union) 규칙 : if X -> Y aand X -> Z, then X -> YZ

     - 분해(Decomposition) 규칙 : if X -> YZ, then X -> Y and X -> Z

     - 유사이행(Pseudo Transitivity) 규칙 : If X -> Y and WY -> Z, then WX -> Z 

       ​

   - 함수 종속성과 기본키

     - 릴레이션 R(K, A1, A2, A3, ...., An)에서 K가 기본키이면, K -> R이 성립. 즉, 기본키는 릴레이션의 모든 속성에 대해 결정자

       ​

3. 정규화

   - 제 1정규형
     - 릴레이션 R의 모든 속성 값은 원자값
   - 제 2 정규형
   - 제 3 정규형
   - BCNF(Boyce )
