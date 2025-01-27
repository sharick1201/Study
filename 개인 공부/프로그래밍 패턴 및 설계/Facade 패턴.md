[[0. Index of 프로젝트]] UMC 7th 프로젝트를 하는 중, 사용자의 프로필을 불러 올 때 다른 도메인들의 정보를 이것저것 끌어와야 하는 상황에서, 코드 리뷰로 컨트롤러에서 타 도메인 정보를 불러오는 것보다 FacadeService로 분리하여 진행하면 컨트롤러의 비즈니스 로직을 줄일 수 있다는 피드백을 받았다. 이에 대해 공부하고자 문서 개설하였다.

---

#### Facade 패턴이란?
![[스크린샷 2025-01-27 오전 10.31.14.png]]
* 구조에 대한 디자인 패턴 중 하나
* 주로 객체 지향 프로프래밍에서 활용된다.
* 복잡한 시스템의 내부 구조를 단순화하여 상위 수준의 인터페이스를 제공한다.
* 클라이언트가 서브시스템의 세부 사항을 알 필요 없이 기능을 사용할 수 있게 된다.



#### 장단점
* 장점
	* 클라이언트-서브시스템 간 의존성을 줄여 낮은 결합도를 보인다.
	* 복잡한 호출 과정을 숨김으로써 가독성이 향상된다.
	* 단순화된 인터페이스를 제공한다.
* 단점
	* 퍼사드 클래스가 너무 많은 책임을 가지면 시스템이 비효율적으로 동작할 수 있다.
	* 모든 기능이 퍼사드에 포함되면 서브시스템의 유연성이 제한될 가능성이 있다.
	* 퍼사드가 앱의 모든 클래스에 결합된 God 객체가 될 수 있다
	* 퍼사드 클래스 자체가 서브시스템에 대한 의존성을 가지게 되어 의존성을 완전히는 피할 수 없다.

따라서 추상화하고자 하는 시스템의 복잡도와 퍼사드 패턴의 이점, 유지보수 비용을 고려하여 결정하는 것이 좋다.



#### 적용해보자!
##### 기존 코드

```
    @Operation(summary = "내 프로필 조회 API", description = "마이페이지를 통해 접근할 수 있는 내 프로필을 조회해오는 API입니다. member의 id, nickname, gender, age, introduction, 대표 프로필 사진, 특정 member가 작성한 게시글 수, 특정 member의 매칭 횟수를 불러옵니다. ")
    @GetMapping("/profile")
    public ApiResponse<MemberResponseDto.GetMypageMemberProfileResultDto> getMypageMemberProfile (@RequestParam Long memberId) {

        Member retrievedMember = memberQueryService.getMemberById(memberId)
                .orElseThrow(()-> new MemberHandler(ErrorStatus.MEMBER_NOT_FOUND));

        MemberProfileImage latestPublicMemberProfileImage = memberProfileImageQueryService.getLatestPublicMemberProfileImage(memberId)
                .orElseThrow(()-> new MemberProfileImageHandler(ErrorStatus.MEMBERPROFILEIMAGE_NOT_FOUND));

        long postCount = postQueryService.getPostCountByMemberId(memberId);

        long succeedApplicationCount = applicationQueryService.getSucceedApplicationCountByMemberId(memberId);

        return ApiResponse.onSuccess(MemberConverter.toGetMemberProfileResponseDto(retrievedMember, latestPublicMemberProfileImage, postCount, succeedApplicationCount));
    }

```
컨트롤러 코든데 비즈니스 로직이 너무 많다! Facade 로직으로 바



---

참고 자료:
- https://refactoring.guru/ko/design-patterns/facade_
- https://inpa.tistory.com/entry/GOF-💠-퍼사드Facade-패턴-제대로-배워보자