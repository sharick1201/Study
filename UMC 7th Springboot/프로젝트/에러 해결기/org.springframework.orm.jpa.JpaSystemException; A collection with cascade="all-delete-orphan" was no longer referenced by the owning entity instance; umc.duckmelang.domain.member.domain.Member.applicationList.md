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
나는 회원의 정보를 업데이트하는 방식을 아래와 같이 작성하였는데, 
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




### 해결