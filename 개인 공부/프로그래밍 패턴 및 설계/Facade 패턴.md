[[0. Index of 프로젝트]] UMC 7th 프로젝트를 하는 중, 사용자의 프로필을 불러 올 때 다른 도메인들의 정보를 이것저것 끌어와야 하는 상황에서, 코드 리뷰로 컨트롤러에서 타 도메인 정보를 불러오는 것보다 FacadeService로 분리하여 진행하면 컨트롤러의 비즈니스 로직을 줄일 수 있다는 피드백을 받았다. 이에 대해 공부하고자 문서 개설하였다.

---







참고 자료:
- https://refactoring.guru/ko/design-patterns/facade_