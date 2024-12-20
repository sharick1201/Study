[[5주차) JPA 기초 및 프로젝트 구조]] 공부 중 보다 더 자세히 알고 싶어서 문서 개설

---

함께 보면 좋은 문서:
[[생성자]]

----

> lombok 공식 문서 > @NoArgsConstructor, @RequiredArgsConstructor, @AllArgsConstructor
> https://projectlombok.org/features/constructor

* Lombok의 생성자 관련 주석들이다.
* Java에서 반복적이고 지루한 생성자 작성 작업을 자동화한다.
* 특정 필드에 대해 1개의 매개변수를 받아 해당 필드에 단순히 할당하는 생성자를 생성한다.

### @NoArgsConstructor
* 매개변수가 없는 생성자를 생성한다.
* final 필드가 존재하는 상태라서 매개변수 없이 초기화할 수 없는 경우, 컴파일 오류가 발생한다.
	* `@NoArgsConstructor(force = true)`: 모든 final 필드를 0 / false / null로 초기화하는 옵션 → 나중에 값을 반드시 설정해야 한다.
* @NonNull과 같이 제약 조건이 있는 필드는 초기화 검사를 생성하지 않는다. → 나중에라도 초기화되지 않으면 제약 조건이 충족되지 않을 수 있다. 주의하자. 
	* <span style="background:rgba(240, 107, 5, 0.2)">그럼 어떻게 해결하는게 좋은가?</span>
	* <font color="#a5a5a5">초기화 검사가 정확히 뭐지?</font>
		* @NonNull이 붙은 필드가 있다면, 실제로 그 필드가 null이 아닌지 확인하고, 아니라면 에러를 내야 한다.
			* 여기서 "실제로 그 필드가 null이 아닌지 확인"하는 작업을 초기화 검사라고 한다.
* 왜 씀? Hibernate나 Service Provider Interface(SPI)와 같은 Java 구성 요소는 매개변수가 없는 생성자를 요구하는 경우가 많으므로. 이 주석은 주로 `@Data` 또는 다른 생성자 생성 주석과 함께 쓰인다.
	* <font color="#a5a5a5">Hibernate, SPI는 왜 매개변수 없는 생성자를 요구하는가? </font>
		* Hibernate: 엔티티 객체를 생성할 때 리플렉션을 사용해서 기본 생성자를 호출한다. 이때 기본 생성자가 없으면 Hibernate가 객체를 생성할 수 없다.
		- SPI: Java에서 서비스 로더나 다른 구성 요소를 통해 객체를 로드할 때, 기본 생성자를 요구하는 경우가 많다.

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
* 음? 전반적으로모르겟음 머라하는지...