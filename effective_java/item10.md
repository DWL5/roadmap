## 아이템10. equals는 일반 규약을 지켜 재정의하라

### equals는 아래와 같은 상황 중 하나에 해당한다면 재정의 하지 않는게 최선이다.

- 각 인스턴스가 본질적으로 고유하다.
- 인스턴스의 `논리적 동치성(logical equlity)` 를 검사할 일이 없다.
- 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어 맞는다.
- 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이없다.
- `인스턴스 통제 클래스` 라면 equals를 정의하지 않아도 된다.
  - `Enum` 이 여기 해당된다.
  - 어차피 논리적으로 같은 인스턴스가 2개 이상 만들어지지 않으니 논리적 동치성과 객체 식별성이 사실상 똑같은 의미가 된다.

### 그럼 언제 equlas를 재정의 하는게 좋을까?

- `객체 식별성` (두 객체가 물리적으로 같은가)가 아니라 `논리적 동치성`을 확인해야 하는데, 상위 클래스의 equals가 논리적 동치성을 비교하도록 재정의되지 않았을 때다.
- 주로 값 클래스(VO)들이 여기에 해당 된다.
  - 두 값 객체를 equals로 비교하는 프로그래머는 객체가 같은지가 아니라 값이 같은지를 알고 싶어 할 것이다.
- equals가 논리적 동치성을 확인하도록 재정의해두면, 인스턴스 끼리 값비교가 가능하고, `Map`의 키와 `Set`의 원소로 사용할 수 있게 된다.

### equals 메서드를 재정의할 때는 반드시 일반 규약을 따라야 한다.

- equals 메서드는 동치관계(equivalence relation)을 구현하며, 다음을 만족한다.
- 반사성(reflexivity) : null이 아닌 모든 참조 값 x에 대해 x.equals(x)는 true다.
- 대칭성(symmetry) : null이 아닌 모든 참조 값 x,y에 대해, x.equals(y)가 true면 y.equals(x)도 true다.
- 추이성(transitivity) : null이 아닌 모든 참조 값 x,y,z에 대해 x.equals(y)가 true이고, y.equals(z)도 true면 x.equals(z)도 true다.
- 일관성(consistency): null이 아닌 모든 참조 값 x,y에 대해 x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.
- null-아님 : null이 아닌 모든 참조 값 x에 대해, x.equals(null)은 false다.

### equals의 대칭성이 위배된 코드

- CaseInsensitiveString는 equals에서 CaseInsensitiveString 뿐만아니라 String과도 비교를 시도

```java
public final class CaseInsensitiveString {
	private final String s;
	
	public CaseInsensitiveString(String s) {
		this.s = Objects.requireNonNull(s);
	}
	
	@Override
	public boolean equals(Object o) {
		if (o instanceof CaseInsenstiveString)
			return s.equalsIgnoreCase(
				((CaseInsensitiveString) o).s);
    if (o instanceof String) {
      return s.euqalsIgnoreCase((String) o);
    return false;
	}
}
```



- cis.equals(s)는 true를 반환한다.
- 그러나 String의 equals는 CaseInsensitiveString의 존재를 모른다.

```java
CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
String s = "polish"
```



### 추이성이 위배되는 경우

- 추이성은 첫 번째 객체와 두 번째 객체가 같고, 두 번째 객체와 세 번째 객체가 같다면, 첫 번째 객체와 세 번째 객체도 같아야 한다는 뜻이다.

```java
public class Point {
	private final int x;
	private final int y;
	
	public Point(int x, int y) {
		this.x = x;
		this.y = y;
	}
	
	@Override public boolean equals(Object o) {
		if (!(o instanceof Point))
			return false;
    Point p = (Point)o;
    return p.x == x && p.y == y;
	}
  ...
}
```

- 상위 클래스에는 없는 새로운 필드를 하위 클래스에 추가하는 상황을 생각해보자.
- 위의 Point 클래스를 확장해서 ColorPoint를 작성해보자.

```java
public class ColorPoint extends Point {
	private final Color color;
	
	public ColorPoint(int x, int y, Color color) {
		super(x, y);
		this.color = color;
	}
	...
}
```

- Point클래스의 equals는 색상 정보는 무시한 채 비교를 수행한다.

```java
@Override public boolean equals(Object o) {
	if (!(o instanceof ColorPoint))
		return false;
  return super.equals(o) && ((ColorPoint) o).color == color;
}
```

- 위의 코드는 대칭성을 위반한다.
- 일반 Point를 ColorPoint에 비교한 결과와 그 둘을 바꿔 비교한 결과가 다를 수 있다.

```java
Point p = new Point(1, 2);
ColorPoint cp = new ColorPoint(1, 2, Color.RED);
```

- p.equals(cp)는 true를 cp.equals(p)는 false를 반화한다.

```java
@Override public boolean equals(Object o) {
	if (!(o instanceof Point)) 
		return false;
    
  if (!(o instanceof ColorPoint)) {
  	return o.equals(this);
  }
  
  return super.equals(o) && ((ColorPoint) o).color == color;
}
```

- 위의 방식은 대칭성은 지켜 주지만, 추이성을 깨버린다.

```java
ColorPoint p1 = new ColorPoint(1, 2, Color.RED);
Point p2 = new Point(1, 2);
ColorPoint p3 = new ColorPoint(1, 2, Color.BLUE);
```

- p1.equals(p2)와 p2.equals(p3)는 true를 반환
- p1.equals(p3)는 false를 반환

```apl
구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않는다.
객체 지향적 추상화의 이점을 포기하지 않는 한은 말이다.
```

- equals 안의 instanceof 검사를 getClass 검사로 바꾸면 규약도 지키고 값도 추가하면서 구체 클래스를 상속 할 수 있을까?

```java
@Override public boolean equals(Object o) {
	if (o == null || o.getClass() != getClass())
		return false;
  Point p = (Point) o;
  return p.x == x && p.y == y;
}
```

- 이번 equals는 같은 구현 클래스의 객체와 비교할 때만 true를 반환

- 하지만 위의 방법은 Point의 하위 클래스가 어디서든 Point로써 활용되지 못한다.

  

### 리스코프 치환원칙

- 어떤 타입에 있어 중요한 속성이라면 그 하위 타입에서도 마찬가지로 중요하다.
- 따라서 그 타입의 모든 메서드가 하위 타입에서도 똑같이 잘 작동해야 한다.

```apl
Point의 하위 클래스는 정의상 여전히 Point이므로 어디서든 Point로써 활용될 수 있어야 한다.
```

- 예를 들어 주어진 점이 단위(반지름 1) 원안에 있는지를 판별하는 메서드가 필요하고 가정해보자.

```java
private static fianl Set<Point> unitCircle = Set.of(
				new Point(1, 0), new Point(0, 1),
				new Point(-1, 0), new Point(0, -1));
				
public static boolean onUnitCircle(Point p) {
		return unitCircle.contains(p);
}
```



```java
public class CounterPoint extends Point {
		private static final AtomicInteger counter = new AtomicInteger();
		
		public CounterPoint(int x, int y) {
			super(x, y);
			counter.incrementAndGet();
		}
		public static int numberCreated() { return counter.get(); }
}
```

- ConterPoint의 인스턴스를 onUnitCircle 메서드에 넘긴다면
  - Point 클래스의 equals를 getClass를 사용해 작성했다면 onUnitCircle은 false를 반환
- 컬렉션 구현체에서 주어진 원소를 담고 있는지 확인하는 방법은 equals 메서드를 이용하는 것 이다.



### 상속보다는 컴포지션

- 구체 클래스의 하위 클래스에서 값을 추가할 방법은 없지만 "상속과 컴포지션을 사용하라" 조언을 따라보자.
- Point를 상속하는 대신 Point를 ColorPoint의 private 필드로 두고, ColorPoint와 같은 위치의 일반 Point를 반환하는 view 메서드를 public으로 추가하는 식이다.

```java
public class ColorPoint {
	private final Point point;
	private final Color color;
	
	public ColorPoint(int x, int y, Color color) {
		point = new Point(x, y);
		this.color = Object.requireNonNull(color);
	}
	
	public Point asPoint() {
		return point;
	}
	
	@Override public boolean equals(Object o) {
		if (!(o instanceof ColorPoint))
			return false;
    ColorPoint cp = (ColorPoint) o;
    return cp.point.equals(point) && cp.color.equals(color);
	}
	...
}
```



### 일관성

- 두 객체가 같다면 (어느 하나 혹은 두 객체 모두가 수정되지 않는 한) 앞으로도 영원히 같아야 한다는 뜻
- 가변 객체는 비교 시점에 따라 서로 다를 수도 혹은 같을 수도 있지만, 불변 객체는 한번 다르면 끝까지 달라야 한다.
- 클래스가 불변이든 가변이든 equals의 판단에 신뢰할 수 없는 자원이 끼어들게 해서는 안된다.
- `java.net.URL`의 equals는 주어진 URL과 매핑된 호스트의 IP주소를 이용해 비교한다.
  - 호스트 이름을 IP주소로 바꾸려면 네트워크를 통해야 하는데, 그 결과가 항상 같다고 보장할 수 없다.
  - equals는 항시 메모리에 존재하는 객체만을 사용한 결정적 계산만 수행해야 한다.

### null-아님

- 모든 객체가 null과 같지 않아야 한다.



### 양질의 euquals 메서드 구현하기

- == 연산자를 사용해서 입력이 자기 자신의 참조인지 확인한다.
- instanceof 연산자로 입력이 올바른 타입인지 확인한다.
- 입력을 올바른 타입으로 형변환한다.
- 입력 객체와 자기 자신의 대응되는 '핵심' 필드들이 모두 일치하는지 하나씩 검사한다.
  - float과 double은 `Float.compate()` , `Double.compate()` 로 비교한다.
  - float과 double을 특별 취급하는 이유는 Float.Nan, -0.0f, 특수한 부동소수 값등을 다뤄야 하기 때문이다.
- equals를 다 구현했다면, 대칭적인가, 추이성이 있는가, 일관적인가에 대해 자문해보자.
- equals를 재정의할 땐 hashCode도 반드시 재정의하자



```api
꼭 필요한 경우가 아니면 equals를 재정의하지 말자. 
많은 경우에 Object의 equals가 여러분이 원하는 비교를 정확히 수행해준다.
재정의해야 할 때는 그 클래스의 핵심 필드를 모두 빠짐없이, 다섯 가지 규약을 확실히 지켜가며 비교해야한다.
```

