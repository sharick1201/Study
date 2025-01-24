[[@NoArgsConstructor & @AllArgsConstructor & 직렬화와 역직렬화]] 공부 중에, DTO나 VO처럼 데이터를 담는 객체를 생성할 때 @AllArgsConstructor를 사용하면 좋다는 문구를 보고... DTO, VO 공부를 좀 더 파고 들어가보고 싶다고 생각되어 문서 개설

---


### DTO



### VO
* Value Object
* 데이터의 값을 나타내는 객체

##### 특징
* 객체의 불변성을 보장하는 경우가 많다. VO가 한 번 생성되면 값을 변경할 수 없다.
	* <span style="background:rgba(240, 107, 5, 0.2)">경우가 많다는 건... 아닌 경우도 있다는 것인가?</span>
* 두 객체를 비교할 때, 객체의 참조(reference)가 아니라 값(value)을 기준으로 비교된다.
* 주로 도메인 모델에서 사용된다.

<span style="background:rgba(240, 107, 5, 0.2)">사용 예시를 보아야 확실히 이해될 거 같다.</span>

### DTO와 VO, 뭐가 다른가?
* DTO는 계층 간 데이터 전송(예: 컨트롤러 → 서비스 → DAO)을 목적으로 사용
	* <span style="background:rgba(240, 107, 5, 0.2)">DAO?</span>
* VO는 특정 도메인에서 의미 있는 값을 표현하기 위한 객체로, 주로 비즈니스 로직에서 사용
* DTO는 가변의 성격을 가진 클래스이고, VO는 값 그 자체의 의미를 가진 불변 클래스(Read-only)이다.
DTO는 인스턴스 개념, VO는 리터럴 개념