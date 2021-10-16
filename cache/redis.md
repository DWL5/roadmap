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

## Redis 복제
- Redis가 만약 단일 인스턴스로 구성되어있다면, Redis의 장애가 모든 Application에 영향을 미친다.

### Master/Replica 구조
- Master/Replica 구성시 Master의 모든 데이터는 모든 Replica에 복제 -> 어떤 Reids 인스턴스에 데이터를 조회해도 원하는 결과 얻을 수 있음
- 데이터의 변경이 발생한다면 변경 작업은 Master에서만 가능
- 변경된 데이터는 비동기적으로 모든 Replica에 전달
- replica-read-only옵션으로 replica를 읽기전용으로 만들 수 있다.

### Mater/Replica 구조의 문제점
- Master에 장애가 발생했을 때, 읽기는 가능 (모든 Redis 인스턴스에 데이터가 복제되어 있으므로) 그러나 쓰기는 수행이 되지 않는다.
- Master 장애 발 생시, 수동으로 Replica중 하나를 Master로 선정하고, 나머지 Replica에서 Master를 바라도록 해야한다.
- 자동으로 FailOver가 되지 않는다.

### Redis Sentinel의 등장
- Sentinel은 별도의 프로세스로 Master 인스턴스 다운시, 이를 감지하여 Replica 중 하나를 Master 인스턴스로 Failover 및 Application에게 통지하는 기능을 포함
- Sentinel은 다른 Sentinel을 포함한 모든 Redis 인스턴스와 연결 이후 1초마다 HearBeat 통신을 통해 Master 및 Replica 서버가 정상적으로 작동중인지 여부를 확인하고 이상 발생시 자동으로 Failover 및 Application에 알림을 전송
- Master에 장애가 감지 되면 Master선출 권한이 있는 Sentinel이 Replica 인스턴스 중 하나를 Master로 승격
- 이후 이전 Master가 다시 살아나도, 기존 Master는 Replica로 자동으로 변경된다.

### Failover
- 시스템 대체 작동
- 평소 사용하는 서버와 그 서버의 클론 서버를 가지고 있다가 사용서버가 장애로 사용이 어렵게 되었을 경우 클론 서버로 그 일을 대신 처리하게 해서 무정지 시스템을 구축하게 해 주는 것을 의미.

## REF
- [레디스 복제](https://cla9.tistory.com/101?category=821397)

