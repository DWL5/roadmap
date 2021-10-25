# INDEX
- 데이터를 조회할 때 (특히 SELECT) 빠르게 접근하도록 도와주는 것

- 테이블 생성 시에 제약 조건으로 Primary Key 또는 Unique를 사용하면 인덱스가 자동 생성된다.
  - Primary Key -> Clustered Index
  - Unique -> Non-Clustered Index

## 인덱스 내부 구조
### B-Tree(Balanced Tree 균형트리)
- 조회 시 성능이 좋아지나, 데이터 변경작업 (INSERT, UPDATE, DELETE)시에 성능이 나빠진다.
- 특히 INSERT 작업이 일어날 때 성능이 급격히 느려질 수 있다. 그 이유는 '페이지 분할' 이라는 작업이 발생되기 때문

![image](https://user-images.githubusercontent.com/18106839/137593124-4102fb93-4564-4a15-86b2-2b9cbd481957.png)

### 클러스터형 인덱스
- 클러스터형 인덱스의 생성 시에는 데이터 페이지 전체가 다시 생성된다.
- 클러스터형 인덱스는 인덱스 자체의 리프 페이지가  곧 데이터다. 그러므로 인덱스 자체에 데이터가 포함되어 있다고 볼 수 있다.
- 클러스터형 인덱스는 보조 인덱스보다 검색속도가 빠르지만 데이터 입력/수정/삭제는 더 느리다.
- 클러스터형 인덱스는 성능이 좋지만 테이블에 한 개만 생성할 수 있다.

### 보조 인덱스 (Non-Clustered Index)
- 보조 인덱스의 생성 시에는 데이터 페이지는 그냥 둔 상태에서 별도의 페이지 인덱스를 구성한다.
- 보조 인덱스의 인덱스 자체의 리프 페이지는 데이터가 아니라 데이터가 위치하는 주소 값이다. 
- 보조 인덱스는 여러개 생성 할 수 있다.