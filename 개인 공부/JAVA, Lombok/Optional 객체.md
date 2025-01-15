이게 뭐임? null이 올 수 있는 값을 감싸는 Wrapper 클래스로, 컨테이너 객체이다. 특정 값이 존재할 수도 있고, 존재하지 않을 수도 있는 경우를 표현한다. Java8부터 지원하고 있다. 
```
public final class Optional<T> {

  // 값이 존재하면 value는 해당 값을 참조한다.
  // 값이 존재하지 않으면 value는 null이다.
  private final T value;
   
  ...
}
```


왜 씀? null값을 안전하게 처리하기 위해서 사용한다. Optional 객체 사용 시 null 체크를 명시적으로 수행할 수 있어서 NullPointerException을 방지한다. 메서드 반환 값에서 값이 없음을 명확히 표현할 수 있기 때문에 코드의 가독성을 높일 수 있다.

`Optional<T>`: T 타입의 값을 포함할 수 있으며, 값이 없을 수도 있는 경우를 나타낸다.

#### 주요 메서드
- `isPresent()`: 값의 존재 여부를 확인한다. 존재하면 true, 존재하지 않으면 false.
- `get()`: 값이 존재할 경우 그 값을 반환한다. 존재하지 않을 경우 `NoSuchElementException`이 발생한다.
- `ifPresent(Consumer<? super T> action)`: 값이 존재할 경우 주어진 작업을 수행한다.
- `orElse(T other)`: 값이 존재하지 않을 경우 대체 값을 반환한다.
- 객체 생성: `Optional<String> opt = Optional.ofNullable("객체생성");`

#### 사용 예시
##### 데이터베이스 조회
```
public Optional<User> findUserById(Long id) {
    return userRepository.findById(id);
}
```
* `userRepository.findById(id)`를 호출하여 데이터베이스에서 특정 ID를 가진 사용자를 조회하고, 그 결과를 Optional로 감싸서 반환한다.
	* 사용자가 존재하는 경우에는 `Optional.of(user)` 형태로 사용자를 감싸서 `Optional<User>` 를 반환한다.
	* 사용자가 존재하지 않는 경우에는 `Optional.empty()` 형태인 `Optional<User>` 를 반환한다.

##### 외부 API 응답 처리
```
public Optional<Product> getProductById(Long id) {
    // API 호출
    Product product = apiClient.getProduct(id);
    return Optional.ofNullable(product);
}
```


#### 주의사항
* 필드나 컬렉션 타입으로 Optional을 사용하는 것은 피하는 게 좋다.
	* Optional을 컬렉션으로 사용한다면(예: `List<Optional<User>>`)
		* 각 요소에 대해 Optional 객체가 생성되므로 메모리와 성능에 부담을 준다. 컬렉션의 크기가 커질수록 이런 비용은 증가한다.
	* Optional을 필드로 사용한다면
		* 객체의 상태를 변경할 수 있는 가능성이 생기므로 불변성이 깨질 위험이 생긴다.
		* 이 필드를 사용할 때마다 `isPresent()`와 `get()` 메서드를 호출해야 하므로 복잡한 로직을 유발한다.
```
if (user.isPresent()) {
	User actualUser = user.get();
}
```

* Optional을 사용하는 것이 성능에 영향을 줄 수 있다. Optional은 객체이기 때문이다.
	* 객체는 기본적으로 추가적인 메모리 할당이 필요하다.
	* 기본형(예: `int`, `long`)과 함께 사용할 때는 박싱(Boxing)과 언박싱(Unboxing) 과정이 필요한데, 이는 성능에 부정적인 영향을 미친다. 특히 반복문이나 대량의 데이터를 처리할 때 성능 저하를 유발한다.
	    - 박싱: 기본형을 객체로 변환하는 과정
	    - 언박싱: 객체를 기본형으로 변환하는 과정

---
#### 참고자료
https://mangkyu.tistory.com/70
https://www.tcpschool.com/java/java_stream_optional