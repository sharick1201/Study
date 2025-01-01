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
	
	* 연관 관계 매핑
		* 연관 관계 주인이란? 실제 테이터베이스에서 외래키를 가지는 엔티티를 의미한다.
			* 외래 키를 가진다는 것은? 다른 엔티티와의 관계를 정의할 수 있다는 것. 외래키를 통해 다른 테이블(엔티티)의 레코드를 참조할 수 있기 때문이다.
			* ex. 학생과 수업 엔티티가 있다. 학생 엔티티가 수업 엔티티를 참조하는 경우, 학생이 연관관계의 주인이다.
			* 1:N의 경우, N에 해당하는 테이블이 외래 키를 가진다(연관 관계의 주인이다)
				* @ManyToOne 어노테이션을 사용한다.
					* @ManyToOne(fetch=FetchType.LAZY) 옵션은 지연 로딩을 설정하는 것인데, 이건 6주차에서 더 배운다.
			* 1:1의 경우, 원하는 테이블에 외래 키를 주면 된다.


		* 단방향 매핑
			* 연관 관계의 주인에게만 연관 관계를 주입하는 것
		* 양방향 매핑
			* 연관 관계의 주인이 아닌 엔티티에도 연관 관계를 주입하는 것(연관 관계를 맺는 엔티티 둘 다에게 주입하는 것)
			* 양방향 매핑을 하면, 객체 그래프 탐색이 가능해진다. → 9주차에서 더 배운다
				* 객체 간의 관계를 기반으로 하여, 특정 객체에서 시작하여 연결된 다른 객체들로 이동하는 과정을 의미한다.
				* ex. 학생과 수업 엔티티가 양방향으로 매핑되어 있다면, 학생 객체에서 수강 중인 수업을 가져올 수 있고, 수업 객체에서 수강 중인 학생들을 가져올 수 있다.
					* 양방향 매핑이 되어있지 않아서, 학생 객체만 수업 객체를 알고 있다면, 수업 내에서 학생을 찾기 위해 추가적인 작업(데이터베이스 쿼리)을 수행해야 한다.
			* 양방향 매핑을 하면, cascade 설정이 가능하다.
				* cascade란?
					* in 데이터베이스
						* 연관 관계의 주인인 테이블에 설정해서 참조 대상인 테이블의 칼럼이 삭제/변경될 때 함께 삭제/변경되는 기능이다.
					* in JPA
						* 연관관계의 주인이 아니라 참조의 대상이 되는 엔티티에 설정해야 한다. → cascade에 단방향이 아니라 양방향 매핑이 필요한 이유
							* 만약 단방향 매핑된 상태에서 cascade할 시, 수업을 하나 삭제할 때 그 수업을 수강하는 학생도 모두 삭제될 수 있다.
			* 양방향 매핑을 하면, 연관 관계 편의 메서드가 필요하게 된다.
				* 연관 관계 편의 메서드란?
					* 연관된 엔티티를 추가하거나 제거할 때, 양쪽 엔티티의 상태를 자동으로 업데이트하는 메서드
				
```
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
    private List<Order> orders = new ArrayList<>();

    // 연관 관계 편의 메서드
    public void addOrder(Order order) {
        orders.add(order);
        order.setUser(this); // 양방향 관계 설정
    }
}

@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "user_id")
    private User user;

    // Getter, Setter
} 
```

* @OneToMany
	* mappedBy
	* CasCadeType.ALL

				
* 칼럼에 대한 세부 설정
		* <font color="#a5a5a5">enum 타입에 붙는, @Column(columnDefinition = "VARCHAR(15) DEFAULT '내용') 이것 뭐지? @Column(length=15) 와의 차이점은 뭐고, 각각 어느 경우에 사용하는 것인가?</font>
			 * 둘 다 enum에 사용 가능하다.
			 * `@Column(nullable = false, length = 40)`
				 * JPA가 사용하는 기본 속성을 사용하여 칼럼의 제약 조건을 설정한다. 데이터베이스 독립적으로 동작한다.
				 * 대부분의 경우 이 방식으로 하면 되는 듯!
				 * 그런데... 이 방식은 기본값 설정을 지원하지 않는다!
			 * `@Column(columnDefinition = "VARCHAR(10)" DEFAULT '내용')`
				 * JPA가 아닌 직접적인 SQL 정의를 사용하여 데이터베이스 컬럼을 설정한다. 즉, JPA가 자동으로 생성하는 DDL을 무시하고, 명시적으로 지정한 정의를 그대로 사용한다.
				 * 데이터베이스에 강하게 의존적이라는 단점이 있다. 특정 DBMS에 종속되기 때문에 이식성이 떨어진다. 대신, SQL로 모든 세부사항을 제어 가능하다.
				 * 기본값 설정을 지원한다.(DEFALUT '내용')
			* 기본값 설정이 필요하다면, columnDefinition을 사용하는 것이 좋겠다.



### 실습

* 테이블, BaseEntity, enum, 매핑 테이블 모두 작성 후 & 연관 관계 매핑 전, 테이블 생성이 모두 잘 되는지 확인하기 위해 첫 실행.
	* 테이블 생성 중 SQLSyntaxError 2번 발생. 프로그램이 작동 중지되지는 않음.
	* MySQLworkbench로 확인해보니, Inquiry, Mission 테이블이 생성되지 않았다.
		* Inquiry 테이블의 경우:
			* Enum 타입의 inquiryCategory 속성 생성 중, `@Column(columnDefinition = "VARCHAR(20)")`  어노테이션 적용에서 문제가 발생한 듯하다. 테이블 생성 로그를 살펴보면, `inquiry_category enum ()` 이라 뜬다. "VARCHAR(20)" 이 제대로 적용되지 않았다.
			* 그러나... 저 문법에는 틀린 게 없었다. 찾아보니 enum은 최소한 하나 이상의 상수를 가져야 해서, 빈 enum은 아무런 의미를 가지지 못하여 발생한 에러였다. 해당 enum 파일에 아무런 값을 넣어두지 않았기 때문에, 에러가 발생하였다.
			* 해결: InquiryCategory enum에 값 하나를 넣어주었다.
		* Mission 테이블의 경우:
			* 로그: `You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'condition varchar(255) not null, end_at datetime(6), point integ' at line 5]`
			* 이건 condition이라는 속성명이 MySQL의 예약어라 발생한 에러이다.
			* 해결: 속성명을 criteria 로 바꿔주었다.

* 테이블 모두 매핑 후 두 번째 실행
	* 실습에서 나오지 않은 1:1 매핑은 @OneToOne 어노테이션을 사용하여 매핑해주었다.
		* 여기에도 fetchType을 적용하는지? 체크해보기
	* 워크북에서 음식 카테고리는 보통 수정/삭제를 하지 않으므로 단방향으로 매핑하겠다고 했는데, 이 부분이 옳을지 생각해보기. 일단 단방향으로 해둠
	* 정상 작동

##### 작업 상태
* InquiryCategory enum에 내용 아직 안 넣어둔 상태
* store 테이블
    - 영업시간이 요일마다 다른 경우를 처리하기 위하여, 영업시간 테이블 별도로 생성
    - 가게의 임시휴무를 처리하기 위하여, 임시휴무 테이블 별도로 생성
	이것들 아직 구현 안 한 상태
* StoreImage, ReviewImage 아직 구현 X
* reviewReply 아직 구현 X
* memberaddress, storeaddress 아직 구현 X
	* 테이블로 빼는 게 더 효율적일지, member, store의 속성으로 넣을지?
* missionAccomplish 매핑 테이블
	* public LocalDateTime endAt; 이거... 넣는 게 좋을지, startAt에서 마감시간까지 계산해서 보여주는게 더 효율적일지? 뭐가 더 효율적이지? 그래서 일단 빼 둠




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
* Entity 클래스의 최대치 제한 어노테이션
	1. JPA의 `@Column(length = 100)` 사용
		* 목적: 테이블 내 컬럼의 크기를 정하는 DDL의 제어
		- 장점:
		    - 데이터베이스 레벨에서 길이 제한을 설정하므로, 데이터 저장 시 자동으로 검증된다.
		    - 성능이 좋고, <u>데이터 무결성을 보장</u>한다.
		- 단점:
		    - 애플리케이션 레벨에서 유효성 검사가 없어서, 클라이언트가 잘못된 데이터를 보내면 예외가 발생할 수 있다.
		    
	2. Java Bean Validation의 `@Size(min = 1, max = 100)` 사용
		* 목적: 필드 크기 검증
		* 사용하기 위해서는 hibernate validator의 dependency를 추가적으로 줘야하는 듯하다.
		* 필드 크기가 min 과 max 사이여야 값을 저장할 수 있도록 유효성을 검사
		- 장점:
		    - <u>애플리케이션 레벨에서 유효성 검사를 수행</u>하므로, 사용자에게 즉각적인 피드백을 제공한다. 따라서 사용자 경험이 향상된다.
		    - 클라이언트 요청이 잘못된 경우 사전에 오류를 처리할 수 있다.
		- 단점:
		    - 데이터베이스에 저장하기 전에 유효성 검사를 수행하므로, 데이터베이스에 잘못된 데이터가 들어가는 것을 방지할 수 없다.
	- 결론:
		- 둘 다 사용하는 것이 가장 좋다.
		- 데이터베이스와 애플리케이션 레벨 모두에서 유효성 검사를 수행하면, 데이터 무결성을 보장하면서 사용자에게도 즉각적인 피드백을 제공할 수 있다.
		- 다만 두 어노테이션에 설정해둔 길이 제한이 동일한지 확인하는 것이 좋겠다.
		- 일단 지금은 데이터 위주의 공부이니, @Column만 작성하고 해보도록 하겠다.

* 양방향 매핑과 단방향 매핑 더 공부해볼 것.


###### 참고자료
* 빌더 패턴
	https://refactoring.guru/ko/design-patterns/builder