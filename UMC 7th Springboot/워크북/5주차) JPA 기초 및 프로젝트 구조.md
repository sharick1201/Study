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

#### 내용 정리 및 추가 공부
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
	* 디렉토리: 
	* 여기서의 컨벤션이란, 작업을 조직적으로 수행하기 위해 공통적으로 합의된 규칙이나 관례를 뜻한다.
	* 디렉토리와 파일의 
	* <span style="background:rgba(240, 107, 5, 0.2)">폴더 구조는 어떻게 짜는 것이 좋으며, 다들 어떤 식으로 짜고 있고 그것을 관리하고 있는가?</span>
	* 

##### 느낀 점, 추가로 더 공부해보고 싶은 것

