## 아이템 1. 생성자 대신 정적 팩터리 메서드를 고려하라

- 클래스는 클라이언트에 public 생성자 대신 정적 팩터리 메서드를 제공할 수 있다.

### 정적 팩터리 메서드의 장점

- 이름을 가질 수 있다.

  - 생성자에 넘기는 매개변수와 생성자 자체만으로는 반환될 객체의 특성을 제대로 설명하지 못한다.
  - 정적 탤터리 메서드는 이름만 잘 지으면 반환 될 객체의 특성을 쉽게 묘사할 수 있다.

  ```java
  BigInteger.probablePrime();
  ```

  - 시그니처가 같은 생성자가 여러 개 필요할 것 같다면, 생성자를 정적 팩터리 메서드로 바꾸고, 각각의 차이를 잘 드러내는 이름을 지어주자.

  ```
  시그니처 - Java에서는 메서드 정의에서 메서드 이름과 매개변수 리스트의 조합을 말한다.
  ```

  

- 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.

  - 불변 클래스는 인스턴스를 미리 만들어 놓거나 새로 생성한 인스턴스를 캐싱하여 재활용하는 식으로 불필요한 객체 생성을 피할 수 있다.

- 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.

  - 반환할 객체의 클래스를 자유롭게 선택할 수 있게 하는 '엄청난 유연성을' 선물 한다.

  - 인터페이스를 정적 팩터리 메서드의 반환 타입으로 사용하는 인터페이스 기반 프레임워크를 만드는 핵심기술이기도 하다.

  - 자바 8 이전에는 인터페이스에 정적 메서드를 선언할 수 없었다.

    - Collections에서 정적팩토리 메서드를 통해 Collection의 구현체를 얻게 해놓았음

    ```java
    static class UnmodifiableCollection<E> implements Collection<E>, Serializable {
            private static final long serialVersionUID = 1820017752578914078L;
    
            final Collection<? extends E> c;
    
            UnmodifiableCollection(Collection<? extends E> c) {
                if (c==null)
                    throw new NullPointerException();
                this.c = c;
            }
      ...
    ```

  - 외부로 클래스들을 공개하지 않고 Collections를 통해 구현체를 제공하는 방법은 API의 외견을 훨씬 작게 만들 수 있었다. -> 질문하기

    

  - 자바 8 이후부터는 인터페이스에 정적메서드 선언이 가능

    ```java
    public interface CollectionTest {
    
        void test();
    
        static CollectionTest getInstance() {
            return () -> System.out.println("오오");
        }
    }
    ```

- 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

  - 반환 타입의 하위 타입기만 하면 어떤 클래스의 객체를 반환하든 상관없다.
  - EnumSet의 생성자는 정적팩토리 메서드로 제공된다.
    - 여기서 클라이언트는 EnumSet의 하위타입이기만 하면 그게 RegularEnumSet인지 JumBoEnumSet인지 상관하지 않아도 된다.

  ```Java
      public static <E extends Enum<E>> EnumSet<E> noneOf(Class<E> elementType) {
          Enum<?>[] universe = getUniverse(elementType);
          if (universe == null)
              throw new ClassCastException(elementType + " not an enum");
  
          if (universe.length <= 64)
              return new RegularEnumSet<>(elementType, universe);
          else
              return new JumboEnumSet<>(elementType, universe);
      }
  ```



- 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.


... 
