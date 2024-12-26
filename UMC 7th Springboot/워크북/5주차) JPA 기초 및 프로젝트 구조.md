##### 목표
* JPA의 개념, 필요성에 대해 이해한다.
* Spring data JPA를 이용하여 Entity 설계, 매핑을 한다.

##### 내용 키워드
* 객체 지향 언어와 관계형 데이터베이스
	* 객체 지향 언어 ≠ RDB 패러다임의 불일치
* JPA
	* ORM(Object Relational Mapping) 기술

* Spring 프로젝트 설계 및 구조
	* Springboot 디렉토리 컨벤션
	* 패키지 소개
	* converter
	* entity 매핑
	* DB 준비
	* domain 패키지에서 entity 만들기
	* Created_at, Updated_at
	* 매핑 테이블 설계
	* 연관 관계 매핑
* 양방향 매핑
* 칼럼 별 세부적인 설정

#### 내용 정리
* 객체 지향 언어 ≠ RDB 패러다임의 불일치
	* 목표
		* 객체 지향 언어인 자바: 캡슐화/상속/다형성을 활용하는 것
			* <span style="background:rgba(240, 107, 5, 0.2)">캡슐화, 상속, 다형성 이게 왜 좋음?</span>
		* RDBMS: 데이터를 정교하게 구성하는 것
* JPA
	* 목적: 반복적인 CRUD 작업을 획기적으로 줄이기 위함
	* 정의:
		* Java Persistence API
			* <span style="background:rgba(240, 107, 5, 0.2)">영속성(Persistence)이란?</span>
		* 자바 진영에서 ORM(Object-Relational Mapping) 기술의 표준으로 사용되는 인터페이스의 모음
			* ORM 기술
				* 애플리케이션의 클래스 - RDB의 테이블을 매핑하는 기술
				* 애플리케이션의 객체를 RDB 클래스에 자동으로 연결시켜 준다.
				* 반복적인 SQL 쿼리문을 작성할 필요 없이 자동으로 매핑하여 날려준다.
			* 인터페이스
				* 실제적으로 구현된 것이 아니라, 프레임워크
				* <span style="background:rgba(240, 107, 5, 0.2)">인터페이스는 뭐고, 프레임워크는 뭐임?</span>
	* 대표적인 오픈소스: Hibernate
		* <span style="background:rgba(240, 107, 5, 0.2)">...은 무엇인가? 좀 더 알아보기</span>

* 디렉토리 컨벤션
	*  디렉토리: 컴퓨팅에서 파일을 분류하기 위해 사용하는 이름 공간. =file, catalog
	* 컨벤션: 작업을 조직적으로 수행하기 위해 공통적으로 합의된 규칙이나 관례.
	* 디렉토리와 파일의 구조를 일정한 규칙에 따라 정리하고 사용하는 방식
	* 가독성, 유지보수성, 협업 효율성을 위해 사용
	* <span style="background:rgba(240, 107, 5, 0.2)">디렉토리 컨벤션은 어떻게 짜는 것이 좋으며, 다들 어떤 식으로 짜고 있고 그것을 관리하고 있는가?</span>
		<font color="#ff0000">	* 디렉토리 컨벤션 내용 공부했던 거 더 정리하기</font>




* Entity 매핑
	* Entity 클래스에 사용되는 주요 어노테이션
		* `@Entity`
			* 해당 클래스가 JPA의 Entity임을 명시
		* `@Getter` / `@Setter`
			* lombok에서 제공
			* 별도의 코드 없이 Getter 및 Setter를 자동 생성
			* <span style="background:rgba(240, 107, 5, 0.2)"> lombok이 뭐지?</span>
			* <span style="background:rgba(240, 107, 5, 0.2)">Getter, Setter 작동 메커니즘이 정확히 뭐지?</span>
		* 빌더 패턴
			* 빌더 패턴을 사용하면 생성자를 사용하는 것보다 더 편리하다
			* `@Builder`
			* `@NoArgsConstructor(access = AccessLevel.PROTECTED)`
			* `@AllArgsConstructor`
	* Primary Key 속성 부여 어노테이션
		* <span style="background:rgba(240, 107, 5, 0.2)">워크북에는 기본 키를 만드는 방법에 여러가지가 있다고 했는데, 구체적으로 어떤 것들이 있고 각각의 장단점은 무엇인지?</span>
		* `@GeneratedValue(strategy = GenerationType.IDENTITY)`
			* JPA가 통신 대상 DBMS의 방식에 따르겠다는 의미(ex. MySQL을 사용하기로 했다면, MySQL의 방식을 따르게 된다. <span style="background:rgba(240, 107, 5, 0.2)">-> 근데.... 뭔 방식을 따른다는거지? 데이터 포맷? 문법?</span>)
	* enum
		* @Enumerated(EnumType.STRING)
			* EnumType의 기본 값은 ORDINAL
				* enum의 순서가 저장된다.
				* enum의 순서를 바꾸게 될 경우 에러 발생하므로 STRING을 사용하는 게 좋다.
	* 모든 엔티티에서 사용하는 속성을 작성하는 효율적인 방법
		* 공통되는 속성들을 작성한 추상 클래스를 만들어주고, 이를 다른 엔티티 클래스에 상속시킨다.
			* <span style="background:rgba(240, 107, 5, 0.2)">추상 클래스에 적힌 @MappedSuperclass, @EntityListeners(AuditingEntityListener.class)는 뭐지?</span>
		* JpaAuditing 사용이 가능하도록, Application 클래스에 @EnableJpaAuditing 어노테이션을 추가한다.
			* JpaAuditing이 뭐지
				* JPA에서 엔티티의 생성 및 수정 시간, 생성자 및 수정자를 자동으로 추적하고 기록하는 기능이다. 데이터베이스의 레코드를 언제, 누가 생성했는지 쉽게 관리할 수 있다.
	* 매핑 테이블 설계


##### 느낀 점, 추가로 더 공부한 것, 더 공부해보고 싶은 것

* [[(미) Lombok]]
* [[(중) Builder 패턴]]
* [[(중) @Builder]]
* [[(중) @NoArgsConstructor & @AllArgsConstructor]]
* [[생성자]]
* [[(미) Hibernate]]
* [[(중) DTO & VO]]
* [[JPA와 Hibernate의 관계]]
* [[Hibernate, SPI는 왜 매개변수 없는 생성자를 요구하는가?]]




###### 참고자료
* 빌더 패턴
	https://refactoring.guru/ko/design-patterns/builder