# Redis
```
메모리 기반의 "키-값" 구조 데이터 관리 시스템이다. 모든 데이터를 메모리에 저장하고 
조회하기에 빠른 Read, Write 속도를 보장하는 비 관계형 데이터베이스이다.
```

- Remote Dictionary Server
- Database, Cache, Messgae Broker
- In-memory, Data Structure Store
- Supports rich data structure

## Cache
- 나중의 요청에 대한 결과를 미리 저장했다가 빠르게 사용하는 것

## 왜 Redis를 쓸까
- JAVA의 자료구조도 인메모리 인데, 왜 Redis를 사용할까?
- 서버가 여러대인 경우 Consistency 문제 발생
  - 서버마다 다른 데이터를 가지고 있게 됨
- Multi-Threaded 환경에서 Race Condition 문제

### Race Condition
- 여러 개의 Thread가 경합하는 것
- Context Switching에 따라 원하지 않는 결과가 발생

### Redis에서는
- Redis는 기본적으로 Single-Threaded
- Redis 자료구조는 Atomic Critical Section에 대한 동기화를 제공
- 서로 다른 Transaction Read/Write를 동기화

### 그럼 어디에 쓸까?
- 여러 서버에서 같은 데이터를 공유 할 때
- Single Server라면 Atomic 자료구조 혹은 Cache를 사용하기 위해 쓸 수 있다.

