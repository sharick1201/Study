<p align="right">2025-01-28</p>
### 상황
선호하는 행사 종류 선택 API, 지뢰 설정 API 에 빈 리스트를 넣으면
```
java.lang.IndexOutOfBoundsException: Index: 0
	at java.base/java.util.Collections$EmptyList.get(Collections.java:4586)
	at umc.duckmelang.domain.member.converter.MemberConverter.toSelectEventResponseDto(MemberConverter.java:69)
```


### 원인
```
  
public static MemberResponseDto.SelectEventsResultDto toSelectEventResponseDto(List<MemberEvent> memberEventList) {  
  
    Member member = memberEventList.get(0).getMember(); // 반환된 리스트 내 모든 MemberEvent는 같은 Member를 참조하고 있음을 전제  
  
    List<Long> eventCategoryIds = memberEventList.stream()  
            .map(memberEvent -> memberEvent.getEventCategory().getId())  
            .toList();  
  
    return MemberResponseDto.SelectEventsResultDto.builder()  
            .memberId(member.getId())  
            .eventCategoryIds(eventCategoryIds)  
            .build();  
  
}
```
여기서 빈 리스트가 온다면, `Member member = memberEventList.get(0).getMember();` 에서 .get(0)의 값을 찾을 수 없어 발생하는 문제이다.

### 해결
빈 리스트가 들어왔을 때, 빈 리스트를 반환하는 로직을 추가하였다.

```
  
public static MemberResponseDto.SelectEventsResultDto toSelectEventResponseDto(List<MemberEvent> memberEventList) {

	// memberEventList가 비어있을 경우  
	if (memberEventList == null || memberEventList.isEmpty()) {  
	    return MemberResponseDto.SelectEventsResultDto.builder()  
	            .memberId(null) // memberId를 null로 설정  
	            .eventCategoryIds(new ArrayList<>()) // 빈 리스트 반환  
	            .build();  
	}
  
    Member member = memberEventList.get(0).getMember(); // 반환된 리스트 내 모든 MemberEvent는 같은 Member를 참조하고 있음을 전제  
  
    List<Long> eventCategoryIds = memberEventList.stream()  
            .map(memberEvent -> memberEvent.getEventCategory().getId())  
            .toList();  
  
    return MemberResponseDto.SelectEventsResultDto.builder()  
            .memberId(member.getId())  
            .eventCategoryIds(eventCategoryIds)  
            .build();  
  
}
	
```
지뢰 설정 Dto에도 동일하게 추가해주었다.

### 결과
성공!!
![[스크린샷 2025-01-28 오전 10.39.47.png]]
![[스크린샷 2025-01-28 오전 10.41.34.png]]
