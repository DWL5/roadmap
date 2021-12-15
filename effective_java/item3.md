## 아이템 3. private 생성자나 열거 타입으로 싱글턴임을 보장하라

### 싱글턴 (Singleton)

- 인스턴스를 오직 하나만 생성할 수 있는 클래스.
- 무상태 객체나 설계상 유일해야 하는 시스템 컴포넌트를 예로 들 수 있다.

```apl
해당 클래스에서 생성되는 인스턴스가 하나임을 보장하는 디자인 패턴이다.
```



### 싱글턴을 만드는 방법

```Java
public class Elvis {
	public static final Elvis INSTANCE = new Elvis();
	private Elvis() {...}
	
	public void leaveTheBuilding() {...}
}
```

- private 생성자는 public static final 필드인 Elbis.INSTANCE를 초기화할 때 딱 한 번만 호출된다.
- Public 이나 Protected생성자가 없으므로 Elvis 클래스가 초기화될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임이 보장된다.
  - 그러나! 리플렉션 API의 `AccessibleObject.setAccessible` 을 사용해 private 생성자를 호출할 수 있다.
  - 이러한 공격을 방어하려면 생성자를 수정하여 두번째 객체가 생성되려 할 때 예외를 던지자

```java
public class Elvis {
	private static final Elvis INSTANCE = new Elvis();
	private Elvis() {...}
	public static Elvis getInstance() { return INSTANCE; }
	
	public void leaveTheBuilding() {...}
}
```



### 싱글턴 클래스를 직렬화 하기

- 싱글턴 클래스에 직렬화 기능을 추가하려면 `implements Serializable` 을 추가하는 것으론 부족하다. 싱글턴 특성을 유지 하려면 모든 필드를 `transient` 로 선언하고, `readResolve 메서드를 추가해야 한다. 그렇지 않으면 역직렬화될 때마다 새로운 객체가 생기게 된다.

```java
class Class implements Serializable {
  private static final transient Class INSTANCE = new Class();
  
  private Class(){...}
  private Class readResolve() { return INSTANCE; }
}
```



- transient
  - Serialize하는 과정에서 제외하고 싶은 경우 선언하는 키워드
- 직렬화
  - 자바 시스템 내부에서 사용되는 Object 또는 Data를 외부의 자바 시스템에서도 사용할 수 있도록 byte 형태로 데이터를 변환하는 기술
  - JVM의 메모리에 상주 (힙 또는 스택) 되어 있는 객체 데이터를 바이트 형태로 변환하는 기술
- 역직렬화
  - byte로 변환된 Data를 원래대로 Object나 Data로 변환하는 기술을 역직렬화라고 부른다.
  - 직려화된 바이트 형태의 데이터를 객체로 변환해서 JVM에 상주시키는 기술



### 열거 타입 방식의 싱글턴

-  public 필드 방식과 비슷하지만, 더 간결하고 추가 노력 없이 직렬화를 할 수 있고, 심지어 아주 복잡한 직렬화 상황이나 리플렉션 공격에서도 제2의 인스턴스가 생기는 일을 완벽히 막아준다.

```java
public enum Elvis {
	INSTANCE;
}

public void leaveTheBuilding();
```

- 만들려는 싱글턴이 `Enum` 외에 다른 클래스를 상속해야 한다면 이 방법은 사용할 수 없다.