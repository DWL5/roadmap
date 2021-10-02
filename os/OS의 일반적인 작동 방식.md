
## 컴퓨터 시스템의 구성

![image](https://user-images.githubusercontent.com/18106839/135725370-e14cc28f-caf8-4897-89be-2b7cfae72331.png)

## 운영체제의 구조

### 커널
- OS의 핵심 부분 (메모리 상주)
- 가장 빈번하게 사용되는 기능들을 담당 (프로세스, 메모리 관리 등)

### 유틸리티(Utility)
- 비상주 프로그램

![image](https://user-images.githubusercontent.com/18106839/135725511-7808c98c-1445-4385-904f-2e11d20774f2.png)


- System Call : System Call은 커널에게 무언가를 요청하는 통로

## 운영체제의 기능

### Process Management
- 프로세스
  - 커널에 등록된 실행단위 (실행 중인 프로그램)

- OS의 프로세스 관리 기능
  - 생성/삭제, 상태관리
  - 자원할당
  - 프로세스 간 통신 및 동기화
  - 교착상태 해결

### Processor Management
- 프로세스 스케줄링
  - 시스템 내의 프로세스 처리 순서 결정 (processor가 처리함)
- 프로세서 할당 관리
  - 프로세스들에 대한 프로세서 할당
  - 한번에 하나의 프로세스만 사용 가능

### MemoeyManament
- 주기억장치
  - 작업을 위한 프로그램 및 데이터를 올려 놓는 공간
- Multi-user, Mutli-tasking 시스템
  - 프로세스에 대한 메모리 할당 및 회수
  - 메모리 여유 공간 관리
  - 각 프로세스의 할당 메모리 영역 접근 보호
- 메모리 할당 방법
  - 전체 적재
  - 일부 적재

### File Management
- 파일
  - 논리적 데이터 저장 단위
- 사용자 및 시스템의 파일관리
- 디렉토리 구조 지원
- 파일 관리 기능
  - 파일 및 디렉토리 생성/삭제
  - 파일 접근 및 조작
  - 파일을 물리적 저장 공간으로 매핑
  - 백업

### I/O Management

![image](https://user-images.githubusercontent.com/18106839/135725849-d9b64008-5440-4c16-ac10-1dfe610761a7.png)
![image](https://user-images.githubusercontent.com/18106839/135725876-ebe6b38d-256f-4275-9b3a-504e18b6a26c.png)


