[[Hibernate, SPI, JPA는 왜 매개변수 없는 생성자를 요구하는가?]]에서 생긴 궁금증으로, 문서 개설

---

JPA
* JAVA 표준 스펙
* 객체를 데이터베이스에 매핑(ORM: Object-Relational Mapping)하기 위한 인터페이스와 규칙을 정의
* JPA는 인터페이스만 제공하며, 실제로 동작하는 구현체는 없다.
	* 즉, JPA는 "이렇게 동작해야 한다"는 규약만 정의


Hibernate
* JPA를 구현한 ORM 프레임워크 중 하나
* JPA의 인터페이스를 구현하여 실제로 동작하는 코드를 제공
* Hibernate는 JPA 외에도 자체적인 확장 기능을 추가로 제공
	* ex. Hibernate Query Language(HQL), 캐싱 등


#### JPA와 Hibernate의 관계
1. JPA는 표준, Hibernate는 구현체
    - JPA는 ORM 동작을 위한 표준 스펙이고, Hibernate는 이 스펙을 구현한 라이브러리이다.
    - Hibernate 외에도 JPA를 구현한 다른 프레임워크들이 존재한다. (ex. EclipseLink, OpenJPA 등)
    
1. **Hibernate는 JPA 스펙을 따르지만, 독립적으로도 사용 가능**:
    - Hibernate는 JPA 없이도 독립적으로 사용이 가능하다. JPA는 단순히 Hibernate의 기능을 표준화한 것에 불과하다.
    - 그러나 JPA를 사용하면 애플리케이션 코드가 특정 구현체(Hibernate)에 종속되지 않으므로, 유연성과 이식성이 높아진다.
	    - <span style="background:rgba(240, 107, 5, 0.2)">이게 무슨 말이지?</span>