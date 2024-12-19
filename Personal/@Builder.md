> lombok 공식 문서 > @builder
> https://projectlombok.org/features/Builder

* 복잡한 객체 생성 과정을 세련되고 간편하게 처리할 수 있다.
* 클래스에 대해 빌더 패턴을 자동으로 생성해주는 도구이다.
* 아래 형태로 객체를 생성할 수 있다.
```
Person person = Person.builder()
				.name("홍길동")
				.age(30)
				.job("개발자") 
				.city("서울") 
				.build();
```
	* 각 필드를 체인 형식으로 설정할 수 있어 가독성이 좋다.
	* 복잡한 생성자 호출 없이 유연하게 객체를 생성할 수 있다.
	
* @Builder 는 클래스, 생성자, 메서드에 붙일 수 있다
	* 클래스: 클래스 전체에 적용하여 해당 클래스의 모든 필드를 대상으로 빌더를 생성
		* 클래스에 `@AllArgsConstructor(access = AccessLevel.PACKAGE)`를 추가하고, 이 생성자에 `@Builder`를 적용한 것처럼 동작한다.
		* 이는 아래 조건을 만족해야 정상적으로 작동한다:
			* 명시적으로 생성자를 작성하지 않았거나,
			* Lombok이 생성자를 생성하도록 허용했을 경우 (@NoArgsConstructor를 사용한 경우 포함)
		* 명시적으로 생성자를 작성했다면, 클래스가 아닌 생성자에 @Builder를 적용해야 한다.
		* **참고**: 클래스에 `@Value`와 `@Builder`를 함께 적용하면, `@Builder`가 생성하려는 패키지-프라이빗 생성자가 `@Value`가 생성하려는 생성자를 덮어쓴다.
			* <span style="background:rgba(240, 107, 5, 0.2)">변수랑 필드의 차이점이 있나? 이렇게 부르는 이유는 뭐지?</span>
		
	* 생성자: 특정 생성자에 적용하여, 해당 생성자를 기반으로 빌더를 생성
		* 이때 생성자는 사실상 정적 메서드처럼 동작하게 된다.
		* 생성자는 특별한 문법으로 호출되는 정적 메서드와 같다. → <span style="background:rgba(240, 107, 5, 0.2)">무슨 말이지?</span>
		* 생성자의 반환 타입: 생성하는 클래스 자체
		* 생성자의 타입 매개변수: 클래스의 타입 매개변수와 동일
		* <span style="background:rgba(240, 107, 5, 0.2)">어떻게? 어떤식으로?</span>
		
	* 메서드: 특정 메서드에 적용하여, 해당 메서드를 호출하는 빌더를 생성

### @Builder가 생성하는 7가지 요소
* @Builder를 메서드에 적용하면 다음 7가지 요소가 자동으로 생성된다.
* String name, int age를 매개변수로 받는 foo라는 메서드에, @Builder를 적용해보자.

	1. **내부 정적 클래스**
		* 메서드 이름에 Builder를 붙인 정적 내부 클래스가 생성된다. <span style="background:rgba(240, 107, 5, 0.2)">(어디에?)</span>
			* ex.
				* 메서드 이름이 foo: FooBuilder라는 이름의 정적 클래스가 만들어진다
				* public static class FooBuilder{}

	2. **필드 생성**
		* Builder 클래스(1.) 안에, 메서드의 각 매개변수에 해당하는 non-static, non-final 필드가 생성된다.
			* <span style="background:rgba(240, 107, 5, 0.2)">non-static, non-final이 의미하는 바가 무엇인가?</span>
			* ex.
				* 메서드 foo(String name, int age) → FooBuilder 클래스 내부에 name과 age 필드가 만들어진다.
				* private String name;
				* private int age;

	3. 기본 생성자
		* Builder 클래스(1.)에 파라미터가 없는 기본 생성자가 생성된다.
			* public static class FooBuilder {
					FooBuilder() {} //package-private 기본 생성자
				}
				* <span style="background:rgba(240, 107, 5, 0.2)"> package-private가 무엇인가?</span>

	4. Setter-like 메서드
		* Builder 클래스(1.)에 생성된다.
		* 각 필드에 값을 설정할 수 있는 메서드가 생성된다.
		* 메서드는 필드 이름과 동일한 이름을 가진다. 값을 설정한 후, 빌더 자신(this)을 반환한다.
			* 이를 통해 체이닝 방식으로 메서드를 호출할 수 있다.
				* <span style="background:rgba(240, 107, 5, 0.2)">체이닝 방식이 무엇인가?</span>

	5. build() 메서드
		* Builder 클래스(1.)에 생성된다.
		* 여기서 생성된 build() 메서드는 원래 메서드(여기서는 foo)를 호출한다. 빌더 클래스의 필드 값을 메서드의 매개변수로 전달한다.
		* build() 메서드는 원래 메서드가 반환하는 타입을 반환한다.
			* public Foo build() { return foo(name, age); // 원래 메서드 호출 }
		* <span style="background:rgba(240, 107, 5, 0.2)"> 이 메서드가 하는 일이 뭔가?</span>

	6. toString() 메서드
		* Builder 클래스(1.)에 생성된다.
		* Builder 클래스의 필드 값을 포함한 문자열을 반환한다.
			* @Override
				public String toString() { 
					return "FooBuilder(name=" + this.name + ", age=" + this.age + ")";
				}
				* <span style="background:rgba(240, 107, 5, 0.2)">여기 @Override 어노테이션이 붙는 이유는?</span>
				* <span style="background:rgba(240, 107, 5, 0.2)"> 이건 어디에 어떻게 쓰임?</span>

	7. buillder() 메서드
		* 원래 메서드가 정의된 클래스에 생성된다.
		* 정적 메서드로 생성된다.
		* 빌더 객체를 생성하고 반환한다.
			* public static FooBuilder builder() { return new FooBuilder(); }
		* <span style="background:rgba(240, 107, 5, 0.2)">이거는 코드가 명시되지는 않던데, 어디에 어떻게 위치하고 있길래 작동할 수 있는 것인가?</span>

##### @Builder가 생성하는 요소 예시
* 원래 메서드가 정의되어 있던 클래스
```
import lombok.Builder;

public class Example {

	@Builder
	public Foo(String name, int age) {
	return "Hello, " + name + "! You are " + age + " years old.";
	}

	public static FooBuilder builder() {       //7
	    return new FooBuilder();
	}
}
```

* 어노테이션으로 인해 생성된 Builder 클래스(1.)
```
public static class FooBuilder {               //1
	private String name;                       //2
	private int age;                           //2
 
	Foobuilder(){}                             //3

	public FooBuilder name(String name) {      //4
		this.name = name;
		return this;
	}
	
	public FooBuilder age(int age) {           //4
		this.age = age;
		return this;
	}

	public Foo build() {                       //5
		return foo(name, age);
	}

	@Override
	public String toString() {                 //6
		return "FooBuilder(name=" + this.name + ", age=" + this.age + ")";
	}
}
```
##### 예시 실행
```
public static void main(String[] args) {
	String greeting = Example.builder()
								.name("Alice")
								.age(25)
								.build(); 
	System.out.println(greeting);
}
```
* 출력 결과:
	`Hello, Alice! You are 25 years old`


* @Builder가 생성하는 7가지 요소 중, 이미 존재하는 요소는 생성을 자동으로 생략한다.
	* ex.
		* 클래스에 이미 FooBuilder라는 클래스가 있다면, Lombok은 FooBuilder 클래스(1.)를 새로 생성하지 않고 이미 존재하는 FooBuilder 클래스에 필드와 메서드를 추가하고자 한다.
		* 이때, 이미 존재하는 FooBuilder 클래스에 @Builder가 생성하고자 하는 필드나 메서드도 이미 있다면 Lombok은 해당 필드나 메서드를 새로이 생성하지 않는다.

* Builder 클래스에는 Lombok의 다른 어노테이션을 사용할 수 없다.
	* 왜? Lombok의 다른 어노테이션이 Builder 클래스를 처리하는 방식과 충돌하는 경우를 방지하기 위해서이다.

* Builder 클래스 이름은 기본적으로, <반환 타입>Builder 형식으로 생성된다.
	* ex.
		* 클래스 이름이 `com.yoyodyne.FancyList<T>`라면, 빌더 이름은 `FancyListBuilder<T>`가 된다.
		- 메서드가 `void`를 반환할 경우, 빌더 이름은 `VoidBuilder`가 된다.

#### @Builder의 옵션
- **빌더 클래스 이름** (기본값: 반환 타입 + `Builder`)
- **`build()` 메서드 이름** (기본값: `build`)
- **`builder()` 메서드 이름** (기본값: `builder`)
- **`toBuilder()` 사용 여부** (기본값: 사용 안 함)
	- `@Builder(toBuilder = true)`를 적용하면, 해당 클래스에는 `toBuilder()`라는 인스턴스 메서드가 생성된다.
	* `toBuilder()` 는 현재 인스턴스의 모든 값을 포함하는 새로운 빌더를 생성한다.
- **모든 생성 요소의 접근 수준** (기본값: `public`)
- **빌더의 setter 메서드에 접두어 추가 여부** (권장하지 않음)  
    예: `Person.builder().setName("Jane").build()` 대신 `Person.builder().name("Jane").build()` 형식.

* 모든 옵션 변경한 경우의 예시
```
@Builder(
	builderClassName = "HelloWorldBuilder",
	buildMethodName = "execute",     
	builderMethodName = "helloWorld",     
	toBuilder = true,     
	access = AccessLevel.PRIVATE,     
	setterPrefix = "set"
)
```

### 추가 어노테이션
#### @Builder.ObtainVia
* 왜 쓰는가? 이를 통해 해당 값을 다른 방식으로 얻을 수 있다.
	* ex. 특정 메서드를 호출하여 값을 설정하도록 설정할 수 있다.
	* <span style="background:rgba(240, 107, 5, 0.2)">추가설명, 예시 필요</span>
* 어느 경우에? @Builder를 생성자나 메서드에 적용하는 경우에 사용 가능하다.
* 어떻게? 매개변수 또는 필드에 @Builder.ObtainVia 어노테이션을 추가하면 사용 가능하다.


#### @Builder.Default
* @Builder만 사용 시, 특정 필드 또는 매개변수가 빌드 과정에서 설정되지 않은 경우 기본값으로 0, null, false가 설정된다.
* 왜 쓰는가? 내가 설정되지 않는 필드/매개변수의 기본값을 커스터마이징하고 싶다면, @Builder.Default를 사용하여 바꿀 수 있다.
* 어떻게? 클래스에 @Builder를 적용한 경우, 필드에 기본값을 직접 지정하고, @Builder.Default를 추가한다.
	* ex. `@Builder.Default private final long created = System.currentTimeMillis();`
* 주의:
	* `@NoArgsConstructor`와 같은 Lombok 생성자를 호출하면 `@Builder.Default`로 설정된 기본값이 유지된다. 그러나 명시적인 생성자를 작성한 경우, 기본값이 설정되지 않으므로 값을 수동으로 설정하거나 Lombok 생성자를 호출해야 한다.
		*<span style="background:rgba(240, 107, 5, 0.2)"> 어떻게?</span>
		
#### @Singular
* 왜 쓰는가?
	* 컬렉션 타입 필드(List, Set, Map 등)에 빌더 패턴을 사용할 수 있게 된다.
	* 아래 메서드가 자동으로 생성된다.
		* 단일 요소(하나의 값)를 추가하는 메서드
			* ex. String job이라는 속성이 있을 때, `addJob(String job)`
		* 다중 요소(여러 값)를 추가하는 메서드
			* ex. `addJobs(Collection<String> jobs)`
		 * 초기화 메서드
			* ex. `clearJobs()`
			* <span style="background:rgba(240, 107, 5, 0.2)">자동으로 컬렉션 타입 필드로 저장이 되는 건가? 하나의 요소만 있더라도?</span>
		* 참고: setter 메서드(컬렉션을 통째로 설정 또는 교체)는 생성되지 않는다.
			→ 컬렉션 자체를 덮어쓰는 작업은 불가능하다.

* 어떻게? 컬렉션 필드나 매개변수에 @Singular를 추가한다.
```
@Builder
public class Example {
    @Singular
    private final List<String> items;
}

Example example = Example.builder()
    .item("Item1")  // 단일 요소 추가
    .item("Item2")  // 또 다른 단일 요소 추가
    .build();
```

* 빌더에서 생성된 컬렉션의 특징
	* Immutable(불변)하다. → 컬렉션은 수정 불가능하다
		* <span style="background:rgba(240, 107, 5, 0.2)">수정 가능하게 만드는 방법은 없나?</span>
		* <span style="background:rgba(240, 107, 5, 0.2)">수정 가능하게 만드는 게 좋은 케이스들에는 뭐가 있을까? 그러니까 수정 가능하게 하는 방법을 모색하는게 의미가 있냐는 취지에서 나온 질문.</span>
	* build() 이후 추가 메서드나 clear() 메서드를 호출해도 이미 생성된 객체에는 영향을 줄 수 없다. 대신, 다시 build()를 다시 호출하면 새로운 컬렉션이 생성된다.
	* 컬렉션은 **효율적이고 최소한의 메모리**를 사용하도록 최적화된 형태로 생성된다.
		* <span style="background:rgba(240, 107, 5, 0.2)">어떻게?</span>


* 지원되는 컬렉션 타입
1. `java.util` 패키지:
    - `Iterable`, `Collection`, `List`:  
	    일반적으로 `ArrayList` 기반의 불변 컬렉션으로 생성
	- `Set`, `SortedSet`, `NavigableSet`:  
	    `HashSet` 또는 `TreeSet` 기반의 불변 컬렉션으로 생성 → <span style="background:rgba(240, 107, 5, 0.2)">HashSet, TreeSet이 모지</span>
	- `Map`, `SortedMap`, `NavigableMap`:  
	    `HashMap` 또는 `TreeMap` 기반의 불변 컬렉션으로 생성됩니다. → <span style="background:rgba(240, 107, 5, 0.2)">HashMap, TreeMap은 또 모지</span>
2. 구아바(Guava)의 `com.google.common.collect` 패키지:
    - `ImmutableCollection`, `ImmutableList`, `ImmutableSortedSet`,  `ImmutableMap`, `ImmutableBiMap`, `ImmutableSortedMap`, `ImmutableTable`
		구아바의 빌더 기능을 활용하여 생성
		<span style="background:rgba(240, 107, 5, 0.2)">나중에 구아바도 좀 공부해보고 싶구나</span>


* 컬렉션 이름 규칙
	- 자동 단수화(Singularization)
	    * `@Singular`이 적용된 컬렉션 이름이 영어 복수형일 경우, Lombok은 이를 자동으로 단수형으로 변환하여 단일 요소 추가 메서드 이름을 생성한다.
		    * ex.
			    `@Singular`
			    `private List<String> statuses;`
		    → `addStatus(String status)`라는 메서드 생성됨
	    
	- 명시적 단수 이름 지정
	    * 영어 단수화 규칙이 적용되지 않거나, 이름이 모호한 경우 Lombok이 오류를 발생시킨다.
	    * 이 경우 단수 이름을 명시적으로 지정해야 한다.
		    * ex.
				`@Singular("axis")
				`private List<Line> axes;`
		    → `addAxis(Line axis)`라는 메서드 생성됨
    
### 옵션
#### 1. `ignoreNullCollections`
-  `addAll()` 메서드에 `null` 컬렉션을 전달하면 `NullPointerException`이 발생한다.
- 하지만 `@Singular(ignoreNullCollections = true)`를 설정하면, `null` 컬렉션은 무시된다. →즉, 아무 작업도 하지 않은 채로 메서드가 종료된다.

#### 2. `setterPrefix`
- 빌더 메서드 이름에 접두어를 추가할 수 있다.
    * ex. `setterPrefix = "with"`로 설정하면 다음 메서드들이 생성된다:
	    - `withName`: 단일 요소 추가
	    - `withNames`: 다중 요소 추가
	    - `clearNames`: 컬렉션 초기화

---

### 예제

#### 1. 기본 사용 예제
```
import lombok.Builder;
import lombok.Singular;
import java.util.List;

@Builder
public class Example {     
	@Singular
	private List<String> items;
}

// 빌더 사용
Example example = Example.builder()
						.item("Item1")  // 단일 요소 추가
						.item("Item2")  // 또 다른 단일 요소 추가
						.build();
						
System.out.println(example);
```
#### 2. 단수 이름 명시
```
`import lombok.Builder; import lombok.Singular; import java.util.List;  @Builder public class Example {     @Singular("axis")     private List<String> axes; }  // 빌더 사용 Example example = Example.builder()     .axis("X")  // 단일 요소 추가     .axis("Y")  // 또 다른 단일 요소 추가     .build();`
```
#### 3. `ignoreNullCollections` 사용
```
import lombok.Builder; 
import lombok.Singular; 
import java.util.List;  

@Builder
public class Example {     
	@Singular(ignoreNullCollections = true)     
	private List<String> items;
}

// 빌더 사용
Example example = Example.builder()
						.items(null)  // null 컬렉션 전달 → 무시됨
						.item("Item1") // 단일 요소 추가 
						.build();
```
