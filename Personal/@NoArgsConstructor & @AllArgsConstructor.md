> lombok 공식 문서 > @NoArgsConstructor, @RequiredArgsConstructor, @AllArgsConstructor
> https://projectlombok.org/features/constructor

* Lombok의 생성자 관련 주석들이다.
* Java에서 반복적이고 지루한 생성자 작성 작업을 자동화한다.
* 특정 필드에 대해 1개의 매개변수를 받아 해당 필드에 단순히 할당하는 생성자를 생성한다.
	* <span style="background:rgba(240, 107, 5, 0.2)">무슨뜻이지</span>

### @NoArgsConstructor
* 매개변수가 없는 생성자를 생성한다.
* final 필드가 존재하는 상태라서 매개변수 없이 초기화할 수 없는 경우, 컴파일 오류가 발생한다.
	* `@NoArgsConstructor(force = true)`: 모든 final 필드를 0 / false / null로 초기화하는 옵션 → 나중에 값을 반드시 설정해야 한다.
* 왜 씀? Hibernate나 Service Provider Interface(SPI)와 같은 Java 구성 요소는 매개변수가 없는 생성자를 요구하는 경우가 많으므로. 이 주석은 주로 `@Data` 또는 다른 생성자 생성 주석과 함께 쓰인다.
	* <span style="background:rgba(240, 107, 5, 0.2)">어떤 구성 요소?</span>

### @AllArgsConstructor
* 클래스의 모든 필드를 초기화하는 생성자를 만든다.
* 왜 씀? - 모든 필드를 한 번에 초기화해야 할 때 유용하다. 
	* ex. DTO나 VO처럼 데이터를 담는 객체를 생성할 때
		* <span style="background:rgba(240, 107, 5, 0.2)">VO가 정확히 뭐지</span>
	* 