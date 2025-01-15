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
- `get()`: 값이 존재할 경우 그 값을 반환한다.
- `ifPresent(Consumer<? super T> action)`: 값이 존재할 경우 주어진 작업을 수행한다.
- `orElse(T other)`: 값이 존재하지 않을 경우 대체 값을 반환한다.

#### 사용 예시
##### 데이터베이스 조회
```
public Optional<User> findUserById(Long id) {
    return userRepository.findById(id);
}
```
* `userRepository.findById(id)`를 호출하여 데이터베이스에서 특정 ID를 가진 사용자를 조회하고, 그 결과를 Optional로 감싸서 반환한다.
	* 사용자가 존재하는 경우에는 `Optional.of(user)` 형태로 반환된다.
	* 사용자가 존재하지 않는 경우에는 `Optional.empty()` 형태로 반환된다.

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
* Optional을 사용하는 것이 성능에 영향을 줄 수 있다.

---
#### 참고자료
https://mangkyu.tistory.com/70
https://www.tcpschool.com/java/java_stream_optional