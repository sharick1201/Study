<p align="right">2025-01-28</p>
### 상황
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