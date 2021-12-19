## 아이템 7. 다 쓴 객체 참조를 해제하라

- 자바는 가비지 컬렉터를 통해 사용하지 않는 객체의 메모리를 알아서 회수해준다.
- 그러나 개발자가 메모리 관리에 더 이상 신경을 쓰지 않아야 하는 것은 아니다.

```java
public class Stack {

    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        this.elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        this.ensureCapacity();
        this.elements[size++] = e;
    }

    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }

        return this.elements[--size];
    }

    /**
     * Ensure space for at least one more element,
     * roughly doubling the capacity each time the array needs to grow.
     */
    private void ensureCapacity() {
        if (this.elements.length == size) {
            this.elements = Arrays.copyOf(elements, 2 * size + 1);
        }
```

- 위의 코드에서는 `메모리 누수`가 발생한다.
- `pop()` 을 통해 꺼내온 객체를 프로그램에서 더 이상 사용하지 않더라도, 스택이 그 객체들의 참조를 가지고 있기 때문에 메모리가 회수 되지 않는다.
- 객체 참조 하나를 살려두면 가비지 컬렉터는 그 객체뿐 아니라 그 객체가 참조하는 모든 객체 (그리고 또 그 객체가 참조하는 모든 객체..)를 회수하지 못한다.
- 위의 스택은 메모리 관리를 직접 한다. elements 배열로 저장소 풀을 만들고, 원소들을 관리한다.
- size보다 작은 Index(활성영역)은 사용되고, 비활성 영역은 사용되지 않는다. 그러나 비활성영역이 사용되지 않는 다는 사실을 개발자만 알고, 가비지 컬렉터는 해당 사실을 알 수가 없다.



```java
  public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }

        Object value = this.elements[--size];
        this.elements[size] = null;
        return value;
    }
```

- 해법은 참조를 다 썼을 때, null 처리 (참조 해제)를 하면 된다.



### 그러나 객체 참조를 null 처리하는 일은 예외적인 경우여야 한다.

- 다 쓴 참조를 해제하는 가장 좋은 방법은 그 참조를 담은 변수를 유효 범위(scope) 밖으로 밀어내는 것 이다.
- 그러나 자기 메모리를 직접 관리하는 클래스 (위의 stack 예시)는 항시 메모리 누수에 주의해야한다.
  - 원소를 다 사용한 즛시 그 원소가 참조한 객체들을 다 null 처리해줘야 한다.



## 캐시

- 캐시 역시 메모리 누수를 일으키는 주범이다.
- 객체 참조를 캐시에 넣고 나서, 이 사실을 까맣게 잊은 채 그 객체를 다 쓴 뒤로도 한참을 그냥 놔두는 일이 있다.



### WeakHashMap

- Key에 해당하는 객체가 더이상 사용되지 않는다고  (null처리) 판단되면 제거한다.

- WeakReference

  ```java
  WeakReference<Integer> weak = new WeakReference<Integer>(prime);
  ```

  - Prime == null이 되면 (해당 객체가 가르키는 참조가 WeakReference 뿐일 경우) GC의 대상이 된다.
  - 메모리가 부족하지 않더라도 GC 대상이 된다.

### LinkedHashMap

- removeEldestEntry

  - 가장 오래된 Entry를 삭제

  

## 리스너와 콜백

- 메모리 누수의 주범
- 클라이언트가 콜백을 등록만 하고, 명확히 해지하지 않는다면 콜백은 계속 쌓여갈 것이다.
- 이럴 때 콜백을 약한 참조로 저장하면 가비지 컬렉터가 즉시 수거해간다.
  - WeakHashMap

