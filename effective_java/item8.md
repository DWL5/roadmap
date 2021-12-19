## 아이템 8. finalizer와 cleaner 사용을 피하라

### finailzer와 cleaner란

- 자바에서 제공하는 객체 소멸자
- finalizer는 예측할 수 없고, 상황에 따라 위험할 수 있어 일반적으로 불 필요하다
- cleaner는 finalizer보다는 덜 위험하지만, 여전히 예측할 수 없고, 느리고, 일반적으로 불필요하다.



### finalizer와 cleaner의 단점

- finalizer와 cleaner는 즉시 수행된다는 보장이 없다.
  - 즉, finalizer와 cleaner로는 제때 실행되어야 하는 작업은 절대 할 수 없다.
  - finializer나 cleaner를 얼마나 신속히 수행할지는 전적으로 가비지 컬렉터 알고리즘에 달렸으며, 이는 가비지 컬렉터 구현마다 천차만별이다.
- 상태를 영구적으로 수정하는 작업에서는 절대 finalizer나 cleaner에 의존해서는 안 된다.
  - 데이터베이스 같은 공유 자원의 영구 락(Lock) 해제를 finalizer나 cleaner에 맡겨 놓으면 분산시스템 전체가 서서히 멈출 것이다.
- finalizer와 cleaner는 심각한 성능 문제도 동반한다.
  - `AutoCloseable` 객체를 생성하고 가비지 컬렉터가 수거하기 까지
    - `try-with-resource` 12ns 걸린다.
    - `finalizer` 550ns 걸린다.
- finalizer를 사용한 클래스는 finalzer 공격에 노출되어 심가한 보안 문제를 일으킬 수도 있다.
  - 객체 생성을 막으려면 생성자에서 예외를 던지는 것만으로 충분하지만, finalizer가 있다면 그렇지도 않다.
  - final이 아닌 클래스를 finalizer 공격으로부터 방어하려면 아무 일도 하지 않는 finalize 메서드를 만들고 final로 선언하자

### AutoCloseable

- 파일이나 스레드 등 종료해야 할 자원을 담고 있는 객체의 클래스에서 finalizer가 cleaner를 대체 할 묘안
- AutoCloseable을 구현 해주고, 클라이언트에서 인스턴스를 다 쓰고 나면 close 메서드를 호출



### finalizer와 cleaner가 쓰일만한 곳

- 자원의 소유자가 close 메서드를 호출하지 않는 것에 대비한 안전망 역할
- 네이티브 피어와 연결된 객체를 사용할 때



```apl
cleaner(자바 8까지는 finalizer)은 안전망 역할이나 중요하지 않은 네이티브 자원 회수용으로만 사용하자. 
물론 이런 경우라도 불확실성과 성능 저하에 주의해야 한다.
```

