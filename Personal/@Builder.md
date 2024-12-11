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
		* <span style="background:rgba(240, 107, 5, 0.2)">필드가 정확히 뭐지</span>
	* 생성자: 특정 생성자에 적용하여, 해당 생성자를 기반으로 빌더를 생성
		* <span style="background:rgba(240, 107, 5, 0.2)">어떻게? 어떤식으로?</span>
	* 메서드: 특정 메서드에 적용하여, 해당 메서드를 호출하는 빌더를 생성

### @Builder가 생성하는 7가지 요소
* @Builder를 메서드에 적용하면 다음 7가지 요소가 자동으로 생성된다.
* String name, int age를 매개변수로 받는 foo라는 메서드에, @Builder를 적용해보자.

	1. **내부 정적 클래스**
		* 메서드 이름에 Builder를 붙인 정적 내부 클래스가 생성된다. <span style="background:rgba(240, 107, 5, 0.2)">(어디에?)</span>
			* ex. 메서드 이름이 foo: FooBuilder라는 이름의 정적 클래스가 만들어진다
				* public static class FooBuilder{}

	2. **필드 생성**
		* Builder 클래스(1.) 안에, 메서드의 각 매개변수에 해당하는 non-static, non-final 필드가 생성된다.
			* <span style="background:rgba(240, 107, 5, 0.2)">non-static, non-final이 의미하는 바가 무엇인가?</span>
			* ex. 메서드 foo(String name, int age) → FooBuilder 클래스 내부에 name과 age 필드가 만들어진다.
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

	7. buillder() 메서드
		* 원래 메서드가 정의된 클래스에 생성된다.
		* 정적 메서드로 생성된다.
		* 빌더 객체를 생성하고 반환한다.
			* 
		* <span style="background:rgba(240, 107, 5, 0.2)">이거는 코드가 명시되지는 않던데, 어디에 어떻게 위치하고 있길래 작동할 수 있는 것인가?</span>

##### @Builder가 생성하는 요소 예시
* 원래 메서드가 정의되어 있던 클래스
```
public class bar {


	public Foo()

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




