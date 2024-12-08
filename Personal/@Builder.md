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

* @Builder가 생성하는 7가지 요소()



