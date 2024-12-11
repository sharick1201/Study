





#### Builder 패턴 구현
1. Lombok 없이 구현
	* 코드가 복잡하다...
```
public class Person {
    private String name;
    private int age;
    private String job;
    private String city;

    private Person(Builder builder) {
        this.name = builder.name;
        this.age = builder.age;
        this.job = builder.job;
        this.city = builder.city;
    }

    public static class Builder {
        private String name;
        private int age;
        private String job;
        private String city;

        public Builder name(String name) {
            this.name = name;
            return this;
        }

        public Builder age(int age) {
            this.age = age;
            return this;
        }

        public Builder job(String job) {
            this.job = job;
            return this;
        }

        public Builder city(String city) {
            this.city = city;
            return this;
        }

        public Person build() {
            return new Person(this);
        }
    }
}
```
* <span style="background:rgba(240, 107, 5, 0.2)">마지막에 build() 메서드는 무슨 역할을 하는 거지?</span>

2. Lombok의 @Builder 사용
	[[@Builder]]
	* 코드가 간단하다
	* Lombok이 자동으로 Builder 패턴 코드를 생성해준다.
```
import lombok.Builder;

@Builder
public class Person {
    private String name;
    private int age;
    private String job;
    private String city;
}
```


* <span style="background:rgba(240, 107, 5, 0.2)">각 패턴 구현 방식의 장단점?</span>




빌더 패턴을 사용해야 하는 이유
https://mangkyu.tistory.com/163

---

#### 추가로 더 공부해야 할 것
* 객체 생성을 위한 패턴에는 다음이 있다
	* 생성자 패턴
	* 정적 메소드 패턴
	* 수정자 패턴
	* 빌더 패턴
	 -> 빌더 패턴 외의 패턴 공부