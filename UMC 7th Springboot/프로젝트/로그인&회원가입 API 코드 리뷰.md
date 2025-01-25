수희님이 작성해주신 로그인&회원가입 API(feature/#9) 코드 리뷰를 진행하게 되면서, 로그인과 회원가입 관련 내용 정리를 위해 문서 개설

---
##### 작업 내용
- Spring Security 설정
- JWT 설정
- 회원가입 API 구현
- 로그인 API 구현
- 토큰 재발급 API 구현
- 로그아웃 API 구현
- 블랙리스트 사용
- 토큰 관련 예외 응답 코드
- Redis 활용하여 구현

#### 0. 시작하기에 앞서
##### Redis 설치
수희님이 redis 설치 후 코드 확인하는게 좋을 거 같다고 말씀해주셔서, Redis를 Docker에 설치하였다.

> [!NOTE] Redis란?
> NoSQL(비관계형 데이터베이스)로, key-value 구조의 비정형 데이터를 저장, 관리하기 위해 사용된다. 인메모리(메모리에 위치한다는 의미) 데이터 구조 저장소이다.
* key-value 구조인 점, 인메모리 데이터 구조인 점이 refresh token을 이용한 access token 재발급 기능에 적합하다.
* 메모리에 위치하기 때문에 빠른 조회, 빠른 쓰기가 가능하다는 장점이 있지만, 메모리에 위치하기 때문에 저장 공간에 제약이 있다.(그러나 redis cluster를 사용해 저장 공간을 확장할 수 있다)
* 추가 공부 자료: https://velog.io/@banggeunho/레디스Redis-알고-쓰자.-정의-저장방식-아키텍처-자료구조-유효-기간

다양한 DB 관리를 위하여 Docker에 컨테이너로 설치하겠다.

1. 터미널의 명령어를 통하여 Redis의 이미지를 다운로드한다.
```
docker pull redis
```

* 이미지 다운로드가 완료되면 docker desktop 어플리케이션에서 확인이 가능하다.
![[스크린샷 2025-01-21 오후 8.33.33.png]]
2. 터미널의 명령어를 통하여 컨테이너를 구축한다.
* 일반적으로 redis는 6379포트에 올리는 듯하다. -> 이 포트가 redis 포트의 기본값이라고 한다.
```
docker run --name redis컨테이너이름 -p 6379:6379 -d redis
```
* `-p`: 포트포워딩(네트워크에서, 외부로부터 들어오는 요청을 특정 내부 IP 주소 및 포트로 전달하는 기술) 옵션
* `-d`: 백그라운드에서 실행시키기 위한 옵션

* 완료 시 docker desktop > containers 에서 확인 가능하다.

##### 코드 리뷰 매너 점검
* 자료: https://gyunny.tistory.com/65#4.%20코드%20리뷰를%20할%20때%20주의할%20점-1*%5C
코드 리뷰는 오류&버그를 찾는 것만이 아니라, 코드의 구조, 설계, 가독성, 유지보수성, 성능 등을 평가하고 피드백을 제공함으로서 전반적인 코드 퀄리티를 향상시키는 작업이다.

###### PR 기본 매너
* 모든 코드는 코드 리뷰가 완료된 후 & 팀 내에서 정한 수만큼의 리뷰어 승인 후 merge 해야 한다.
* PR을 받았다면 하루 내에 답변을 주는 게 좋다.
* 리뷰하기에 양이 많다면, 오프라인 리뷰가 보다 효율적이다.
* 특별히 신경 써서 확인해야 할 리뷰 포인트는 코멘트를 남긴다.
* commit 메세지 성실하게 작성한다.
* PR 본문에 PR을 작성한 계기, 수정 방향, 관련 이슈들을 충분히 작성한다.
* PR은 작을수록 좋다.(리뷰하기 쉽고, 버그 있을 가능성 낮음)

###### 코드 리뷰 포인트
* 설계
	* 일반적인 프로그래밍 설계 지침에 맞춰 코드가 작성되었는가?
	* 사내/팀내 어플리케이션 설계 원칙에 맞춰 코드가 작성되었는가?
* 유지보수성
	* 중요한 값을 하드코딩하지는 않았는지?
	* 주석이 코드가 하는 일이 아닌, <u>코드에 담긴 의도</u>를 설명하고 있는지?
	* 코드를 쉽게 이해할 수 있는지?
		* 코드만 봐도 작성자 의도 파악이 가능한지
		* 비즈니스 로직을 잘 설명하고 있는지
		* 올바른 변수명, 함수명, 클래스명을 사용하는지
		* 내가 이해할 수 없는 코드에 대한 설명 or 개선 요구
	* 설정이 용이한지?
	* 스타일 가이드에 맞게 작성되었는지?
	* 하나의 함수가 10라인을 넘어간다면 너무 많은 관심사를 갖고 있는 건 아닌지 파악
* 재사용성
	* 중복된 코드는 없는지? 두 번 이상 반복해서 등장하는 코드는 함수나 클래스로 분리하는 것 검토
* 안정성
	* 예외 처리를 제대로 하고 있는지? 
	* 자원을 효율적으로 반환하고 있는지?
* 확장성
	* 새로운 기능을 추가하기 쉽게 작성되어 있는지?
	* 함수나 클래스가 두 개 이상의 관심사를 가지고 있지는 않은지?
* 테스트
	* 테스트하기 쉬운지?
		* 큰 함수는 작은 단위로 나눈다
		* 클래스 간에 모킹하기 쉬운 인터페이스로 연결되어 있는지 확인한다
	* 테스트가 작성되어 있는지?

###### 코드 리뷰 방식
1. 전체 코드를 훑어보기
	* 코드 전체에서 일어나는 변경을 가볍게 훑는다
	* 의미 없는 변경이나 변경이 용납되지 않은 곳에 변경이 일어나고 있는지 확인한다.
2. PR의 주안점 확인
	* 리뷰를 받는 사람이 제시한 주요 변경사항 확인하고, 주안점이 제시되어 있지 않은 경우 PR 작성자에게 설명을 요구
	* 구조 변경을 위한 PR은 빠른 피드백과 동료 전달을 요한다.
* 잘한 점은 칭찬한다.
* <u>요청 받은 즉시 하는 것을 원칙으로 한다.</u>
* 의견에 근거를 제시한다. 근거는 권장 사례를 설명한 문서나 공식 문서 등을 활용한다.
* 질문은 구체적으로 한다.
* 부드러운 언어를 구사한다.



#### 1. 시작하기: 실행시켜보기
application-local.yml 파일을 내 환경에 맞게 잠시 수정하고, 어플리케이션을 실행시켰다.
* [[Web server failed to start. Port 8080 was already in use.]] 에러 발생했는데 코드 문제는 아니라 터미널로 해결했다. (잊을만하면 발생하는 아이...)
실행 과정에서 에러가 나지는 않았다. 1월 18일, 카톡으로 지우님이 말씀주신 걸 인용하여 application-local.yml 파일만 gitignore에 추가해달라고 요청드렸다. (로컬 DB 비밀번호 노출 문제 & 개인 로컬 환경이 다 달라서 충돌이 일어난다는 문제)

#### 2. Spring Security 설정
* 내용 참고: [[10주차) 로그인 및 회원 가입]]
* 경로: `duckmelang/domain/auth/security/config/SecurityConfig.java`
##### 상황
* SecurityFilterChain
	* CSRF 보안, 폼 로그인, HTTP 기본 인증을 비활성화하고, 세션 관리를 STATELESS로 설정
	* `JwtAuthorizationFilter`를 `UsernamePasswordAuthenticationFilter` 앞에 추가하여 JWT 기반 인증을 수행
	* 예외 처리를 위해 `CustomAuthenticationEntryPoint` 설정
* AuthenticationManager
	* `AuthenticationConfiguration`을 사용하여 `AuthenticationManager` 생성
* PasswordEncoder
* DaoAuthenticationProvider
	* 이게 뭐지?
		* Spring Security에서 제공하는 인증 제공자 중 하나
		* 사용자 정보를 데이터 엑세스 객체(DAO)에서 조회하여 인증을 수행한다.
		* 사용자 조회, 비밀번호 검증, 인증 객체 생성 기능
		* 이 클래스를 사용하기 위해서는, UserDetailsService 구현체와 PasswordEncoder 설정이 필요하다
	* `daoAuthenticationProvider` 메서드에서 `DaoAuthenticationProvider`를 Bean으로 등록
	* `CustomUserDetailsService`를 사용자 정보 조회 서비스로 설정하고, `passwordEncoder`를 사용하도록 설정
	* `hideUserNotFoundExceptions` 옵션을 false로 설정하여 `UsernameNotFoundException`을 노출
##### 개선점
* DaoAuthenticationProvider
	* 우리 계속 Repository 패턴으로 해왔늗데, 얘도 Repository 패턴으로 할 수는 없을까? 더 알아봐야 할 거 같다.
* .formLogin(formLogin -> formLogin.disable()) 이거 왜 disable 해두었는지? 나머지 로직들을 지켜봐야겠다.
* csrf 활성화? -> 필요 없을듯
	* https://velog.io/@wonizizi99/SpringSpring-security-CSRF란-disable
	*  rest api를 이용한 서버라면, session 기반 인증과는 다르게 stateless하기 때문에 서버에 인증정보를 보관하지 않는다. rest api에서 client는 권한이 필요한 요청을 하기 위해서는 요청에 필요한 인증 정보를(OAuth2, jwt토큰 등)을 포함시켜야 한다. 따라서 서버에 인증정보를 저장하지 않기 때문에 굳이 불필요한 csrf 코드들을 작성할 필요가 없다.

#### 3. JWT 설정





* 로그인 횟수 시도 제한 설정?(워크북 10주차 보안 강화 팁 참고)