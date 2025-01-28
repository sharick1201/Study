<p align="right">2025-01-28</p>
### 상황
회원 프로필 수정 API를 실행시키면, 아래 에러가 뜬다
```
org.springframework.orm.jpa.JpaSystemException: could not execute statement [Connection is read-only. Queries leading to data modification are not allowed] [insert into member_profile_image (created_at,is_public,member_id,member_image,updated_at) values (?,?,?,?,?)]
```

### 원인
코드 리뷰하면서 추가한 Facade Service에서 @Transactional 설정을 readOnly = true 로 해놨기 때문에 발생하였다.
```
@Transactional(readOnly = true)
public MemberResponseDto.GetMypageMemberProfileEditResultDto updateMypageMemberProfile(Long memberId, MemberRequestDto.UpdateMemberProfileDto request) {  
  
    // 회원 기본 정보 조회  
    Member retrievedMember = memberQueryService.findById(memberId)  
            .orElseThrow(()-> new MemberException(ErrorStatus.MEMBER_NOT_FOUND));  
  
    // 회원 기본 정보 수정  
    Member updatedMember = memberCommandService.updateMemberProfile(memberId, request);  
  
    // 프로필 이미지 추가  
    MemberProfileImage updatedMemberProfileImage = memberProfileImageCommandService.createMemberProfile(memberId, request.getMemberProfileImageURL());  
  
    // 대표 프로필 이미지 1개 조회  
    MemberProfileImage latestPublicMemberProfileImage = memberProfileImageQueryService.getLatestPublicMemberProfileImage(memberId)  
            .orElseThrow(()-> new MemberProfileImageException(ErrorStatus.MEMBERPROFILEIMAGE_NOT_FOUND));  
  
  
    return MemberConverter.toUpdateMemberProfileDto(updatedMember,latestPublicMemberProfileImage);  
}
```

### 해결
`@Transactional(readOnly = true)`에서 `readOnly = true` 설정을 제거해주었다.

### 결과
성공~~
![[스크린샷 2025-01-28 오후 2.07.02.png]]