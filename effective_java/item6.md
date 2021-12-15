## 아이템6. 불필요한 객체 생성을 피하라



```java
String a = new String("bikini")
String b = "bikini";
String c = "bikini";
```

- 자바에서는 `스트링 풀` 이 존재하여 같은 가상머신 안에서 이와 똑같은 문자열 리터럴을 사용하는 모든 코드가 같은 객체를 재사용함이 보장된다.

```java
a == b; // false
b == c; // true
```



### 생성자는 호출 할 때마다 새로운 객체를 만들지만, 팩터리 메서드는 그렇지 않다

- 생성 비용이 비싼 객체는 캐싱하여 재사용하길 권한다.
- 예를 들어, String.matches는 정규표현식으로 문자열 형태를 확인하는 가장 쉬운 방법이지만, 성능이 중요한 상황에서 반복해 사용하기엔 적합하지 않다.

```java
static boolean isRomanNumeral(String s) {
  return s.matches("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?x{0,3})(I[XV]|V?I{0,3}$)")
}
```

```java
//String의 matches()
public boolean matches(String regex) {
	return Pattern.matches(regex, this);
}
```

```java
//Pattern의 matches()
public static boolean matches(String regex, CharSequence input) {
	Pattern p = Pattern.compile(regex);
	Matcher m = p.matcher(input);
	return m.matches();
}

public static Pattern compile(String regex) {
  return new Pattern(regex, 0);
}

public Matcher matcher(CharSequence input) {
  if (!compiled) {
    synchronized(this) {
      if (!compiled)
        compile();
    }
  }
  Matcher m = new Matcher(this, input);
  return m;
}
```

- Pattren은 입력받은 정규표현식에 해당하는 유한 상태 머신을 만들기 때문에 인스턴스 생성 비용이 높다.
- 성능을 개선하려면 필요한 정규표현식을 표현하는 Pattern 인스턴스를 클래스 초기화 (정적 초기화) 과정에서 직접 생성해 캐싱해두고, 나중에 `isRomanNUmeral` 메서드가 호출될 때마다 이 인스턴스를 재사용한다.

```java
public class RomanNumerals {
  private static final Pattren ROMAN = Pattren.compile("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?x{0,3})(I[XV]|V?I{0,3}$)");
  
  static boolean isRomanNumeral(String s) {
		return ROMAN.matchers(s).matches();
  }
}

```



### 오토박싱

- 래퍼(Wrapper) 클래스의 객체로 변환하는 것을 말한다.
- 오토박싱은 프로그래머가 기본 타입과 박싱된 기본 타입을 섞어 쓸 때 자동으로 상호 변환해주는 기술이다.
- 오토박싱은 기본 타입과 그에 대응하는 박싱된 기본 타입의 구분을 흐려주지만, 완전히 없애주는 것은 아니다.

```apl
박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않는 오토박싱이 숨어들지 않도록 주의하자
```

```java
private static long sum() {
	Long sum = 0L;
	for (long i = 0; i <= Integer.MAX_VALUE; i++) {
		sum += i;
	}
	
	return sum;
}
```



- 추가로 언박싱이란
  - 래퍼 유형의 객체를 해당 기본 값으로 변환하는 것을 말한다.

```java
Integer a = new Integer(100);
int test = a;
```





### 객체 생성에 대해

- 방어적 복사가 필요한 상황에서 객체를 재사용했을 때의 피해가, 필요 없는 객체를 반속 생성했을 때의 피해보다 훨씬 크다
- 방어적 복사에 실패하면 언제 터져나올지 모르는 버그와 보안 구멍으로 이어지지만, 불필요한 객체 생성은 그저 코드의 형태와 성능에만 영향을 준다.