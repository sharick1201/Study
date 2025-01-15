이게 뭐임? null이 올 수 있는 값을 감싸는 Wrapper 클래스로, 컨테이너 객체이다. 특정 값이 존재할 수도 있고, 존재하지 않을 수도 있는 경우를 표현한다. Java8부터 지원하고 있다. Optional 객체는 

```
public final class Optional<T> {

  // 값이 존재하면 value는 해당 값을 참조한다.
  // 값이 존재하지 않으면 value는 null이다.
  private final T value;
   
  ...
}

```


왜 씀? null값을 안전하게 처리하기 위해서 사용한다. Optional 객체 사용 시 null 체크를 명시적으로 수행할 수 있어서 NullPointerException을 방지한다. 메더 코드의 가독성을 높일 수 있다.

`Optional<T>`: T 타입의 값을 포함할 수 있으며, 값이 없을 수도 있는 경우를 나타낸다.

#### 주요 메서드
- `isPresent()`: 값의 존재 여부를 확인한다.
- `get()`: 값이 존재할 경우 그 값을 반환한다.
- `ifPresent(Consumer<? super T> action)`: 값이 존재할 경우 주어진 작업을 수행한다.
- `orElse(T other)`: 값이 존재하지 않을 경우 대체 값을 반환한다.