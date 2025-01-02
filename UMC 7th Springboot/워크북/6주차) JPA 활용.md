##### 목표
* 즉시 로딩과 지연 로딩의 전략 차이에 대해 알아보고, 지연 로딩을 채택하는 이유에 대해 이해한다.
* JPQL과 QueryDSL의 차이에 대해 이해한다.

##### 내용 키워드
* JPA의 영속성 컨텍스트
* 지연 로딩과 즉시 로딩
* N+1 문제
* JPQL
* QueryDSL

#### 내용 정리
* 영속성 컨텍스트(Persistance Context)
	* 영속성 컨텍스트란?
		* 데이터(엔티티 객체)를 영구적으로 저장하는 일종의 메모리 공간이다.
		* 일종의 캐시 메모리이다.
			* DB 속 데이터 조회 시, 먼저 캐시에 찾는 데이터가 남아 있는지 확인한다. 캐시에 남아있는 데이터라면 DB까지 가지 않고 그걸 들고가고, 캐시에 없다면 DB에서 가져온다.
		* 최초로 엔티티에 접근하면, DB에서 끌어온 엔티티를 영속성 컨텍스트에 등록한다. 이후 JPA에서 Repository 인터페이스를 통하여 같은 엔티티에 접근하면, DB가 아니라 영속성 컨텍스트에 있는 데이터를 조회하게 된다.
		* 쉽게 말해, 애플리케이션과 데이터베이스 사이에서 데이터를 저장하는 가상의 데이터베이스 역할을 한다.
		* @EntityManager를 이용하여 접근한다.
		
	* 왜 영속해야 하는가?
		* 1차적인 캐시 기능을 해준다.
		* 변경 감지(Dirty Checking)를 해줄 수 있다.
			* 트랜잭션이 끝나면, JPA는 영속성 컨텍스트 내부에서 변경된 내용을 자동으로 감지한다. 또한 DB에 UPDATE 쿼리를 날려 변경된 내용을 반영한다.
		* 지연 로딩(Lazy Loading)이 가능해진다.
			* 5주차에서 언급만 하고 넘어간 FetchType.LAZY가 바로 지연 로딩에 대한 부분이다.
			* vs. 즉시 로딩(FetchType.EAGER)
				* Member 엔티티에 Address 엔티티, 장바구니 엔티티가 매핑되어 있다고 해보자.
				* 즉시 로딩은 Member, Address, 장바구니 모두를 실제 DB에서 즉시 한 번에 조회하여, 하나의 쿼리로 가져온다. 즉, 관련된 엔티티를 한 번의 쿼리로 즉시 조회한다.
				* 지연 로딩은 Member, Address, 장바구니 조회를 분리하여... Member만 조회하는 쿼리만 DB로 날린다. Address, 장바구니는 프록시 객체로 생성해둔다. 후에 Address, 장바구니 객체가 사용될 때 그제서야 DB에서 Address, 장바구니 정보를 가져와 프록시 객체를 초기화시킨다.
					* 여기서의 프록시란?
						* 대리, 중계인 역할
						* 특정 개체 호출 시, 프록시 개체를 중간에 두면 이 프록시 객체가 진짜 객체 대신 요청을 받는다.
						* 프록시 객체는 실제 객체를 상속받는다.
						* 처음 사용될 때 한 번만 초기화된다.
			* `@ManyToOne, @OneToOne`의 디폴트: 즉시 로딩
			* `@OneToMany, @ManyToMany`의 디폴트: 지연 로딩
			* 가급적이면 지연 로딩을 사용할 것이 권장된다. 즉시 로딩은 JPQL에서 N+1문제가 발생할 수 있기 때문이다.
			
* N+1 문제
	* 1개의 쿼리를 실행한 후에, 관련된 n개의 데이터를 각각 가져오기 위해 추가적으로 N번의 불필요한 쿼리가 실행되는 문제
		* 모든 Member 회원을 조회하려고 한다. 이때, Member를 조회하는 쿼리 1개와, 각 member의 장바구니를 조회하려는 쿼리 n개가 추가적으로 발생한다. n은 Member테이블에 있는 모든 엔티티의 수이다. 장바구니를 가지고 있지 않은 member가 있다고 하더라도, 그 member의 장바구니를 조회하는 쿼리가 생성된다.
			* SELECT * FROM Cart WHERE memberId = 1; ... 같은 게 n개 발생하는 것이다. 
	* 성능 저하를 발생시킴
	* RDB와 객체 지향 패러다임 사이의 간극으로 인해 발생하는 문제
		* 객체는 연관 관계를 통해 레퍼런스를 가지고 있으면 언제든지 메모리 내에서 Random Access를 통해 연관 객체에 접근할 수 있지만, RDB의 경우 SELECT 쿼리를 통해서만 조회할 수 있기 때문이다.
			* 레퍼런스를 가진다: 참조하는 객체의 메모리 주소를 저장하고 있다.
	* https://incheol-jung.gitbook.io/docs/q-and-a/spring/n+1
		* FetchType.LAZY여도 발생이 가능하다. 연관 관계로 매핑된 객체가 사용될 때 DB에서 정보를 가져오는 것은 동일하기 때문. 발생 시점의 차이가 되겠다.
	* <span style="background:rgba(240, 107, 5, 0.2)">해결 방법들에 대해 찾아보기</span>


* JPQL
	* JPQL이란?
		* JPA의 일부
		* 쿼리를 데이터베이스의 테이블이 아닌 JPA 엔티티, 즉 객체를 대상으로 작성하는 객체 지향 쿼리 언어이다.
			* SQL처럼 데이터베이스와 상호 작용한다는 기본적인 개념은 가져가면서, 객체 지향적인 특성을 반영한 쿼리를 날릴 수 있도록 도와주는 언어.
	* 어떻게 사용하는가?
		1.  EntityManager 인터페이스
			* Native SQL과 병행 사용 가능
			* 트랜잭션 관리, 엔티티 작업 관리에 탁월
			* <span style="background:rgba(240, 107, 5, 0.2)">이것도 공부해보기!!</span>
		2. repository 인터페이스
			* 간결함과 일관성 유지에 탁월
			* 좀 더 일반적으로 사용하고 있음
			* 좀 더 구체적으로, 어떻게 사용하는가?
				1.  메서드 이름으로 쿼리 생성
					* Spring Data JPA는 메서드 이름을 기반으로, 자동으로 쿼리를 생성해준다.
					* ex. findByNicknameAndStatus(String nickname, MemberStatus memberstatus) 메서드를 만들고, ("샤릭",MemberStatus.ACTIVATE)로 실행했다고 하자. 그럼 Spring Data JPA는 내부적으로 아래와 같은 쿼리를 자동적으로 실행해준다.
					 `SELECT * FROM member WHERE nickname = '시은' AND status = 'ACTIVE';`
				2. @Query 어노테이션
					* JPQL을 직접 작성할 수도 있다. 복잡한 조건이 필요하거나 커스터마이징이 필요한 쿼리를 작성하고 싶을 때, 사용 가능하다.
					
					    `@Query("SELECT m FROM Member m WHERE m.name = :name AND m.status = :status")`
					    `List<Member> findByNameAndStatus(@Param("name") String name, @Param("status") MemberStatus status);`
					    1. @Query 어노테이션을 사용하여 만들고 싶은 쿼리를 작성한다.
					    2. @Param 어노테이션을 통해 쿼리에 사용된 :name, :status 파라미터를 메서드의 인자와 연결시킨다.
						    * :은 named parameter라는 것을 나타내는 기호다. 쿼리 파라미터를 이름으로 식별할 수 있다. 순서 기반 파라미터(?0, ?1, ?2) 보다 코드 가독성이 높다.
						    * 아래 조건을 만족하면 `@Param`을 생략할 수도 있다.
								 1. Spring Data JPA 2.0 이상에서, 메서드 파라미터 이름과 Named Parameter 이름이 동일한 경우 (`String name → :name`)
								 2. 파라미터가 하나뿐인 경우
}


### 실습


##### 작업 상태



##### 느낀 점, 추가로 더 공부한 것, 더 공부해보고 싶은 것





###### 참고자료