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


### 해결


### 결과