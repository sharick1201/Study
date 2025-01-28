====[[5주차) JPA 기초 및 프로젝트 구조]] 공부 중 보다 더 자세히 알고 싶어서 문서 개설

---

> lombok 공식 문서 > @NoArgsConstructor, @RequiredArgsConstructor, @AllArgsConstructor
> https://projectlombok.org/features/constructor

* Lombok의 생성자 관련 주석들이다.
* Java에서 반복적이고 지루한 생성자 작성 작업을 자동화한다.
* 특정 필드에 대해 1개의 매개변수를 받아 해당 필드에 단순히 할당하는 생성자를 생성한다.

### @NoArgsConstructor
* 매개변수가 없는 생성자를 생성한다.
	* [[생성자]]
* final 필드가 존재하는 상태라서 매개변수 없이 초기화할 수 없는 경우, 컴파일 오류가 발생한다.
	* `@NoArgsConstructor(force = true)`: 모든 final 필드를 0 / false / null로 초기화하는 옵션 → 나중에 값을 반드시 설정해야 한다.
* @NonNull과 같이 제약 조건이 있는 필드는 초기화 검사를 생성하지 않는다. → 나중에라도 초기화되지 않으면 제약 조건이 충족되지 않을 수 있다. 주의하자. 
	* <font color="#a5a5a5">그럼 어떻게 해결하는게 좋은가?</font>
		* 그래서 클래스의 모든 필드를 초기화해주는 @AllArgsConstructor를 함께 쓴다. (@NoArgsConstructor와 @AllArgsConstructor를 함께 쓰는 이유)
		* 그러나 선택적 초기화나 추가 검증이 필요한 경우에는 `@Builder`나 정적 팩토리 메서드와 같은 다른 방법을 고려한다.
	* <span style="background:rgba(240, 107, 5, 0.2)">@NotNull 말고 다른 제약 조건에는 어떤 것들이 있나?</span>
	* <font color="#a5a5a5">초기화 검사가 정확히 뭐지?</font>
		* @NonNull이 붙은 필드가 있다면, 실제로 그 필드가 null이 아닌지 확인하고, 아니라면 에러를 내야 한다.
			* 여기서 "실제로 그 필드가 null이 아닌지 확인"하는 작업을 초기화 검사라고 한다.
* 왜 씀? Hibernate나 Service Provider Interface(SPI), JPA와 같은 Java 구성 요소와, Spring 또는 Jackson같은 프레임워크는 매개변수가 없는 생성자를 요구하는 경우가 많으므로. → <u>프레임워크와 호환성을 유지하기 위해 사용</u>된다고 보면 되겠다.
	* 이 주석은 주로 `@Data` 또는 다른 생성자 생성 주석과 함께 쓰인다.
	* [[Hibernate]]
	* [[Hibernate, SPI는 왜 매개변수 없는 생성자를 요구하는가?]]
	* Spring 프레임워크는 왜 매개변수 없는 생성자를 요구하는가?
		* 

#### AccessLevel 옵션
접근 제어자를 의미한다.
* (access = AccessLevel.PROTECTED)
	* 생성자의 접근 수준: PROTECTED
	* 같은 패키지 내의 다른 클래스 또는 이 클래스를 상속받은 자식클래스에서만 접근 가능하다.


### @AllArgsConstructor
* 클래스의 모든 필드를 초기화하는 생성자를 만든다.
* 왜 씀? 모든 필드를 한 번에 초기화해야 할 때 유용하다. 
	* ex. DTO나 VO처럼 데이터를 담는 객체를 생성할 때
		* [[DTO & VO]]
```
@AllArgsConstructor
public class Product {
    private String name;
    private double price;
    private int quantity;
}

// 생성된 생성자:
// public Product(String name, double price, int quantity) {
//     this.name = name;
//     this.price = price;
//     this.quantity = quantity;
// }
```


#### staticName 옵션
* `@AllArgsConstructor`에 붙어 있는 옵션
* 생성자를 직접 호출하는 대신 정적 메서드를 통해 객체를 생성할 수 있다.
```
@AllArgsConstructor(staticName = "of")
public class Product {
    private String name;
    private double price;
    private int quantity;
}

// 객체 생성
Product product = Product.of("Laptop", 1500.0, 10);

```
* 왜 씀?
	* 코드 가독성이 향상되고 의미가 보다 명확해진다.
	* 추가적인 로직을 삽입할 수 있게 된다. 정적 메서드 내부에, 객체 생성 전 검증 로직이나 변환 로직을 추가 가능하다.
	* 원하는 이름을 부여할 수 있다. 생성자 이름은 고정되어 있다는 점에서 상반된다.




<span style="background:rgba(240, 107, 5, 0.2)">@RequiredConstructor는 자주 안 쓰이는 걸로 보이는데, 그 이유는 무엇이고, 쓰이는 경우에는 어떤 게 있는가?</span>


---

#### @AllArgsConstructor만 쓰고 있는데, @NoArgsConstructor를 추가해야 할까?

추가해야 하는 경우

- **JPA 엔티티 클래스**인 경우:
    - JPA는 기본 생성자가 필요하므로 `@NoArgsConstructor`를 추가해야 한다.
    - 기본 생성자는 `protected`로 선언하는 것이 일반적이다(JPA 권장 사항).

- **DTO나 VO 객체를 JSON 직렬화/역직렬화에 사용하는 경우**:
    - Jackson 또는 기타 라이브러리를 사용할 때 기본 생성자가 없으면 <u>역직렬화</u>가 실패한다.
    - 이 경우 `@NoArgsConstructor`를 추가해야 한다.

- **테스트 코드에서 빈 객체를 생성해야 하는 경우**:
    - 테스트를 위해 기본 생성자가 필요하다면 추가하는 것이 좋다.


추가하지 않아도 되는 경우

- 만약 해당 클래스가 JPA 엔티티가 아니고, 모든 필드가 필수값이라 항상 `@AllArgsConstructor`를 통해 객체를 생성해야 한다면 `@NoArgsConstructor`는 필요하지 않을 수 있다.
    - 예: 값이 반드시 필요한 불변 객체(Immutable Object)라면 기본 생성자가 필요하지 않을 수 있다.


#### 직렬화, 역직렬화가 뭐야?
* 직렬화(Serialization)
	-  객체(Object)를 바이트 스트림(Byte Stream)으로 변환하는 과정
	- 변환된 바이트 스트림은 파일로 저장하거나 네트워크를 통해 전송할 수 있다.
	- 왜 필요함?
		- 객체는 메모리에서만 존재하며, 메모리 외부(예: 파일, 네트워크)로 데이터를 전송하거나 저장하려면 객체를 변환해야 한다.
		- 직렬화를 통해 객체를 저장 가능한 형태 또는 다른 시스템과 교환 가능한 형태로 변환한다.
	- 예
		- Java에서 객체를 파일에 저장하거나, 네트워크를 통해 다른 서버로 전송할 때 사용
		- JSON, XML, Protocol Buffers 등도 직렬화의 한 형태

- 역직렬화(Deserialization)
	-  바이트 스트림(Byte Stream)을 다시 객체(Object)로 변환하는 과정
	- 직렬화된 데이터를 읽어서 원래의 객체 형태로 복원
	- 왜 필요함?
		- 저장된 데이터나 네트워크를 통해 다시 받은 데이터를 다시 객체로 사용하려면 역직렬화가 필요하다.
		- 직렬화된 데이터를 다시 프로그래밍 언어에서 사용할 수 있는 형태로 복원하는 것

* 활용 사례
	* 파일 저장(사용자 설정, 게임 저장 데이터 등)
	* 네트워크 통신(REST API에서 JSON 직렬화/역직렬화, RPC(Remote Procedure Call))
	* 데이터베이스 저장
	* 메세지 큐(Kafka, RabbitMQ 등을 통해 객체를 전송할 때)



https://minwoo-it-factory.tistory.com/entry/DTO에-Noargs-사용하는이유와-aceessLevel-Protected을-사용하는이유  
> 우선, 기본 생성자가 필요한 이유를 알기 위해서는
> 스프링이 어떻게 Dto를 JSON으로 맵핑하는지 그 원리를 알아야 한다.
> 
> 스프링은 바로 Jackson 라이브러리의 **ObjectMapper를 사용**하여 JSON으로 맵핑한다!
> 
> ObjectMapper는 직렬화(serialize), 역직렬화(deserialize)를 수행하는데,
> 
> 이때 **직렬화는 Java Object → JSON으로 파싱**하는 것,
> **역직렬화는 JSON → Java Object로 파싱**하는 것을 의미한다.
> 
> 컨트롤러에서 DTO를 @RequestBody를 통해 가져올 때, 바인딩을 ObjectMapper가 수행한다.
> 
> 이렇게 ObjectMapper가 직렬화, 역직렬화를 수행하여 맵핑할 때,
> DTO를 DTO의 기본 생성자를 이용하여 생성하게 된다.
> 
> 이러한 과정에서 DTO의 기본 생성자가 없다면, 맵핑 오류가 나는 것이다.



### 추가 의견!

https://m-ioi-m.tistory.com/entry/왜-dto에서-항상-Getter와-NoArgsConstructor를-붙여줘야-할까  
> 그러나 기본 생성자가 없어도 이런 변환작업은 가능하다고 합니다. 
> 
> ObjectMapper 내부에는 property와 생성자가 위임된 경우 그 정보를 이용해서 직렬화/역직렬화에 사용하는 로직이 있습니다.
> 
> 위임 어노테이션에는 @JsonProperty, @JsonAutoDetect, @JsonCreator가 있습니다. 
> 
> 여기서 위임이라는 것은 어노테이션을 사용해 객체를 변환(직렬화/역직렬화)할 때 쓰일 정보를 직접 선언하는 것인데,
> 
> 이 위임을 자동으로 해주는 jackson-datatype-jdk8이라는 라이브러리가 있습니다.
> ![](https://blog.kakaocdn.net/dn/uf6v3/btr5ZkZBf8u/VNFJQuwKIkvVrLvpmuUsB0/img.png)
> 
> 현재 저희의 프로젝트에도 포함되어 있습니다. 위의 3가지 모듈을 모두 지원해주고 있습니다. 
> 
> 여기에서 @JsonProperty 어노테이션을 통해서 jackson-module-parameter-names 모듈을 지원받을 수 있습니다. 
> 
> 이 모듈은 기본 생성자가 없어도 다른 생성자로 대체하여 역직렬화를 수행할 수 있다고 합니다.
> 
> SpringBoot에서는 jackson binding을 할 때 ObjectMapper에 이 모듈이 기본적으로 등록되어 있다고 합니다. 
> 
> 그래서 controller에 들어오는 request dto에 기본생성자가 없어도 역직렬화를 수행할 수 있습니다.

그럼... 이 경우엔 안 써도 되는건가?? -> 근데 해보니까 안 된다 ㅠㅠ