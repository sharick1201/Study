> lombok 공식 문서 > @NoArgsConstructor, @RequiredArgsConstructor, @AllArgsConstructor
> https://projectlombok.org/features/constructor

* Lombok의 생성자 관련 주석들이다.
* Java에서 반복적이고 지루한 생성자 작성 작업을 자동화한다.
* 특정 필드에 대해 1개의 매개변수를 받아 해당 필드에 단순히 할당하는 생성자를 생성한다.
	* <span style="background:rgba(240, 107, 5, 0.2)">무슨뜻이지</span>

### @NoArgsConstructor
* 매개변수가 없는 생성자를 생성한다.
* final 필드가 존재하는 상태라서 매개변수 없이 초기화할 수 없는 경우, 컴파일 오류가 발생한다.