<p align="right">2025-01-28</p>
### 상황
자기소개 문구 설정 API 실행 시 아래 에러가 발생한다.
![[스크린샷 2025-01-28 오전 10.49.48.png]]

```
org.springframework.dao.DataIntegrityViolationException: could not execute statement [Column 'birth' cannot be null] [insert into member (birth,created_at,email,gender,introduction,name,nickname,password,updated_at) values (?,?,?,?,?,?,?,?,?)]; SQL [insert into member (birth,created_at,email,gender,introduction,name,nickname,password,updated_at) values (?,?,?,?,?,?,?,?,?)]; constraint [null]

 ...
 	at umc.duckmelang.domain.member.service.MemberCommandServiceImpl.createIntroduction(MemberCommandServiceImpl.java:183)

```


수희님이 테스트 해주셨을 땐 Column 'password' cannot be null 로 떴다.
```
Caused by: org.hibernate.exception.ConstraintViolationException: could not execute statement [Column 'password' cannot be null] [insert into member (birth,created_at,email,gender,introduction,name,nickname,password,updated_at) values (?,?,?,?,?,?,?,?,?)]
 
```


### 원인

자기소개 업데이트 과정에 문제가 생기는 것 같다. Member 객체의 업데이트 과정에서 기존에 저장되어 있던 필드 값들이 유지가 안 되어서 birth나 password의 notNull 체크에 걸렸을 것이라 예상된다.

관련 로직을 살펴보자. MemberCommandServiceImpl에서 발생했으니 우선 해당 클래스부터.
```
@Override  
@Transactional  
public Member createIntroduction(Long memberId, MemberRequestDto.CreateIntroductionDto request) {  
    // 회원 조회 및 유효성 검증  
    Member member = memberRepository.findById(memberId)  
            .orElseThrow(() -> new MemberException(ErrorStatus.MEMBER_NOT_FOUND));  
  
    // 자기소개 문구 유효성검증  
    if (request.getIntroduction().trim().isEmpty()) {  
        throw new MemberException(ErrorStatus.MEMBER_EMPTY_INTRODUCTION);  
    }  
  
    // 자기소개 업데이트  
    Member updatedMember = MemberConverter.toMemberWithIntroduction(member, request.getIntroduction());  
  
    return memberRepository.save(updatedMember);  
}
```
1. `memberRepository.findById(memberId)`를 통해 회원을 조회
2. 조회된 `Member` 객체의 자기소개(`introduction`)를 업데이트하기 위해 `MemberConverter.toMemberWithIntroduction` 메서드를 호출
3. 업데이트된 `Member` 객체를 `memberRepository.save`로 저장

MemberConverter를 보자. 소개글 업데이트는 Member 도메인 클래스의 withIntroduction()으로 진행되고 있다.
```
public static Member toMemberWithIntroduction(Member member, String introduction) {  
    return member.withIntroduction(introduction);  
}
```

Member의 도메인 클래스를 보면, 엥? 새 객체를 지가 만들고 있다...
```
public Member withIntroduction(String introduction) {  
    Member newMember = new Member(this);  
    this.introduction = introduction;  
    return newMember;  
}
```


### 해결
기존 객체의 필드를 직접 수정하는 방식으로 개선했다.
```
// Member

public void withIntroduction(String introduction) {  
    this.introduction = introduction;  
}
```

### 결과
성공!
![[스크린샷 2025-01-28 오후 1.33.31.png]]