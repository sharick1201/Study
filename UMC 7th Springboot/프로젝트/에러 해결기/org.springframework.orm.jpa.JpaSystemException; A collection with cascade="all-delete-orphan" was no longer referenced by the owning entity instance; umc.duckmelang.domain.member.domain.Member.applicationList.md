### 상황
회원 정보 수정 API를 작성 완료하고 테스트하는 중, 이런 에러가 발생했다.
```
org.springframework.orm.jpa.JpaSystemException: A collection with cascade="all-delete-orphan" was no longer referenced by the owning entity instance: umc.duckmelang.domain.member.domain.Member.applicationList

...
	at umc.duckmelang.domain.member.controller.MyPageController.updateMypageMemberProfile(MyPageController.java:75)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:77)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.base/java.lang.reflect.Method.invoke(Method.java:569)


```

### 원인
해당 에러는 JPA에서 "orphanRemoval" 또는 "cascade = ALL" 설정이 적용된 컬렉션이 더 이상 소유 엔티티에서 참조되지 않을 때 발생한다. 

주로 연관 관계를 관리하는 방식에 문제가 있을 때 발생한다고 한다. 부모 엔티티와 자식 엔티티 간의 관계에서, 자식 엔티티 컬렉션을 잘못 처리했을 때 발생할 가능성이 높다.

좀 더 구체적으로, 컬렉션을 직접 교체하거나, 관계를 제대로 설정하지 않을 때 발생한다.

현재 에러는 memberCommandService.updateMemberProfile에서 발생하고 있다.
나는 회원의 정보를 업데이트하는 방식을 아래와 같이 작성하였는데, 이 부분이 문제가 되고 있었다.
```
// memberCommandService.updateMemberProfile
Member updatedMember = MemberConverter.toUpdatedMember(member, request.getNickname(), request.getIntroduction());


// memberConverter
public static Member toUpdatedMember(Member member, String nickname, String introduction) {  
    return Member.builder()  
            .id(member.getId())  
            .nickname(nickname)  
            .introduction(introduction)  
            .build();  
}
```

이 로직은... `getId()`로 기존 ID를 유지하고, 다른 필드만 업데이트하는 것처럼 보이지만, JPA 관점에서 이는 수정이 아니라 **새로운 객체 생성으로 간주**된다. 이 차이점이 문제의 핵심인듯 하다.

* 왜 새로운 객체 생성으로 간주되는가?
	* JPA는 엔티티의 영속성 컨텍스트(Persistence Context)를 기반으로 엔티티를 관리한다. 즉, 특정 엔티티가 영속성 컨텍스트에 포함되어 있어야 JPA가 해당 엔티티를 "수정"으로 인식하고 변경 사항을 자동으로 감지한다.
	* memberConverter에서 builder패턴으로 리턴하는 코드는, 기존 `member` 객체를 수정하는 것이 아니라, 새로운 `Member` 객체를 생성하게 된다. JPA는 새로운 객체를 영속성 컨텍스트와 연관 짓지 못하기 때문에, 이를 "수정"이 아닌 "새로운 객체 삽입"으로 처리하려고 한다.
		* JPA에서 엔티티를 구분하는 기준은 다음과 같다
			* 영속성 컨텍스트에 포함된 엔티티인지에 대한 여부
				* 영속성 컨텍스트에 포함된 엔티티는 JPA가 관리하며, 변경 사항을 감지하여 `UPDATE` 쿼리를 실행
			* @Id 값을 기준으로 동일한 엔티티인지 판단
				* 동일한 `@Id` 값을 가진 엔티티라도, 영속성 컨텍스트에 포함되지 않은 새로운 객체는 기존 엔티티와 별개로 간주

요컨대 새로운 객체를 생성하면 JPA는 이걸 새로운 엔티티로 간주하기 때문에, 기존 ID를 복사하더라도, JPA는 이를 기존 객체와 동일한 엔티티로 인식하지 않는다.

> 따라서 문제를 해결하기 위해서는, **JPA가 해당 로직을 수정으로 인식하도록** 만들어야 한다.


### 해결
JPA가 수정으로 인식하도록 만드는 방법은 다음과 같다.

- 엔티티에 상태 변경 메서드 추가
	- 엔티티 내부에 update메서드를 추가하고, 서비스 계층에서 이룰 호출하여 상태 변경

- 영속성 컨텍스트에서 직접 수정
	- 새로운 객체 생성하지 않고, 서비스 계층에서 영속성 컨텍스트에 포함된 객체를 수정한다.
```
// 직접 필드 값을 수정 (Dirty Checking) member.setNickname(request.getNickname()); member.setIntroduction(request.getIntroduction());
```

- JPQL과 `@Modifying`을 사용하여 직접 업데이트
	- 리포지토리에 쿼리문을 직접 사용
	- 변경 감지를 사용하지 않고 바로 업데이트 쿼리를 실행하는 방식임
```
@Modifying @Query("UPDATE Member m SET m.nickname = :nickname, m.introduction = :introduction WHERE m.id = :id")
void updateProfile(@Param("id") Long id,
	@Param("nickname") String nickname, 
	@Param("introduction") String introduction);
```

엔티티에 Setter 사용을 지양하고(일관성 문제), 가장 객체지향적이면서 JPA의 변경 감지 기능을 효과적으로 활용할 수 있는 방법은 **엔티티에 상태 변경 메서드 추가** 방식이다.

* 왜 이게 가장 좋은 방식이지? 근거는?
	* 객체지향적 설계 원칙에 부합한다는 게 무슨 뜻인가?
		* 객체지향 설계에서 중요한 원칙 중 하나는 **캡슐화(encapsulation)**
			* 엔티티는 자신의 상태와 행동을 스스로 관리해야 한다.
			* 외부에서 직접 상태를 변경하는 것이 아니라, 의도를 명확히 드러내는 메서드를 통해 상태 변경을 수행해야 한다.
			* `updateProfile`과 같은 메서드를 엔티티 내부에 정의하면, 엔티티의 상태 변경 로직이 엔티티 내부로 응집된다. 이는 **"상태와 행동을 함께 묶는다"는 객체지향 설계의 기본 원칙**을 따르는 일이다.
				* 이 방식은 메서드명으로 상태 변경의 의도를 명확히 표현하고
				* 유효성 검증을 내재화함으로써 엔티티의 일관성을 보장할 수 있고
					* 서비스단, 컨트롤러단에 유효성 검증을 두는 것과 뭐가 다른가?
						* 엔티티 
				* 서비스 계층을 단순화할 수 있다.
					* 서비스 계층을 단순화하는 게 좋은가?
					
	* JPA의 변경 감지(Dirty Checking) 활용
		* JPA는 영속성 컨텍스트(Persistence Context)에 포함된 엔티티의 상태를 추적한다. 이를 통해 엔티티의 필드가 변경되었는지 감지하고, 트랜잭션이 종료될 때 자동으로 `UPDATE` 쿼리를 실행한다.
		* 변경 감지가 동작하는 구체적인 방식은 다음과 같다.
			1. 엔티티가 영속성 컨텍스트에 포함되면, JPA는 해당 엔티티의 스냅샷(초기 상태)를 저장한다.
			2. 트랜잭션 내에서 엔티티의 필드 값을 변경하면, JPA는 스냅샷과 현재 상태를 비교하여 변경된 필드를 감지한다.
			3. 트랜잭션이 종료되면, 변경된 필드에 자동으로 `UPDATE` 쿼리를 실행한다.
		* `updateProfile`과 같은 메서드를 엔티티 내부에 정의하면, `updateProfile` 메서드는 기존 엔티티의 필드를 직접 변경하므로, JPA가 변경 사항을 자연스럽게 감지한다.
		* 별도의 `save` 메서드를 호출하지 않아도, JPA가 영속성 컨텍스트에서 상태 변경을 추적하고 자동으로 데이터베이스에 반영한다.
			*  호출이 자원을 낭비하는 일인가?
				* `save` 호출 자체가 자원을 낭비하는 것은 아니다. 그러나 이미 영속성 컨텍스트에서 관리 중인 엔티티에 대해 `save`를 호출하면, 불필요한 병합 작업을 유발할 수 있다.

	* 유지보수성, 테스트 용이성
		* 상태 변경과 관련된 로직을 수정해야 할 때 엔티티 내부만 수정하면 된다.
		* 서비스 계층에서 로직이 분산되지 않아, 서비스 계층의 코드가 단순해지고 가독성이 높아진다.
		* 간단한 단위테스트로 로직을 쉽게 검증할 수 있다.

	* JPQL과 `@Modifying`을 사용하여 직접 업데이트하는 방식은, 엔티티의 상태가 영속성 컨텍스트와 불일치할 수 있다는 위험이 있고, 상태 변경 로직이 엔티티 외부에 존재하므로 로직이 분산되고 객체지향적 설계 원칙에 어긋난다. 또 데이터베이스에 직접 접근하므로 테스트와 유지보수가 어렵다.


> 그럼 이제 엔티티에 상태 변경 메서드를 추가하여 문제를 해결해보자!


위에 작성한 converter 관련 로직을 삭제하고 아래 코드들을 추가했다.
```
//Member entity 클래스

// 프로필 업데이트 메서드 추가
public void updateProfile(String nickname, String introduction) {  
    if (nickname == null || nickname.isBlank()) {  
        throw new MemberHandler(ErrorStatus.MEMBER_EMPTY_NICKNAME);  
    }  
    if (introduction == null || introduction.isBlank()) {  
        throw new MemberHandler(ErrorStatus.MEMBER_EMPTY_INTRODUCTION);  
    }  
    this.nickname = nickname;  
    this.introduction = introduction;  
}



//memberCommandService.updateMemberProfile 내의 상태 변경 메서드 호출을 변경하고 member를 return하는 것으로 변경
@Override  
@Transactional  
public Member updateMemberProfile(Long memberId, MemberRequestDto.UpdateMemberProfileDto request) {  
  
    // 회원 조회 및 유효성 검증  
    Member member = memberRepository.findById(memberId)  
            .orElseThrow(() -> new MemberHandler(ErrorStatus.MEMBER_NOT_FOUND));  
  
    // 상태 변경 메서드 호출  
    member.updateProfile(request.getNickname(), request.getIntroduction());  
  
    return member;  
}
```


결과는... 성공!
![[스크린샷 2025-01-24 오후 4.59.54.png]]