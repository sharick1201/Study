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
		* <span style="background:rgba(240, 107, 5, 0.2)">필드가 정확히 뭐지</span>
		
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


#### @Builder.Default


#### @Singular