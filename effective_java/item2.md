## 아이템 2. 생성자에 매개변수가 많다면 빌더를 고려하라



### 점층적 생성자 패턴과 자바빈즈 패턴

- 점층적 생성자 패턴도 쓸 수 있지만, 매개변수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵다.
  - 코드를 읽을 때 각 값이 무엇인지 헷갈릴 것이다.
    - 인텔리제이에서는 객체를 생성할 때 CMD + P로 인자값 보기를 할 수 있다!

- 매개변수가 없는 생성자로 객체를 만든 후, 세터 메서드를 호출해 원하는 매개변수의 값을 설정하는 방식 -> 자바빈즈 패턴 
  - 객체를 하나 만들려면 메서드를 호출해야 하고, 객체가 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓이게 된다.
    - 생성자에서 유효성 검사를 한다면, 객체가 생성되는 시점의 데이터에 대한 유효성 검증이 가능하나, 세터를 통해 계속 값을 넣게 된다면 객체가 생성되어야 할 때에 전체 데이터에 대한 유효성 검증을 할 수 없다.
  - 자바빈즈 패턴에서는 클래스를 불변으로 만들 수 없다.



### Builder 패턴

- Builder의 생성자와 메서드에서 입력 매개변수를 검사하고, build 메서드가 호출하는 생성자에서 여러 매개변수에 걸친 불변식을 검사하자

```java
public class NutritionFacts {
	private final int servingSize;
	private final int servings;
	private final int calories;
	private final int fat;
	private final int sodium;
	private final int carbohydrate;
	
	public static class Builder {
		private final int servingSize;
		private final int servings;
		
		private int calories = 0;
		private int fat = 0;
		private int sodium = 0;
		private int carbohydrate = 0;
		
		public Builder(int servingSize, int servings) {
			this.servingSize = servingSize;
			this.servings = servings;
		}
    
    public Builder calories(int val) {
      calories = val;
      return this;
    }
    
    public Builder fat(int val) {
      fat = val;
      return this;
    }
    
    public Builder sodium(int val) {
      sodium = val;
      return this;
    }
    
    public Builder carbohydrate(int val) {
      carbohydrate = val;
      return this;
    }
    
    public NuttitionFacts build() {
      return new NutritionFacts(this);
    }
	}
  
  private NutritionFacts(Builder builder) {
    servingSize = builder.servingSize;
    servings = builder.servings;
    calories = builder.calories;
    fat = builder.fat;
    sodium = builder.sodium;
    carbohydrate = builder.carbohydrate;
  }
}
```



### 계층적으로  설계된 클래스와 함께 쓰기 좋은 빌더 패턴

...
