충돌 상황에서는 Git이 충돌 파일에 특정 마커를 추가하며, 이를 수정하고 병합을 완료하면 된다.

#### 1. 병합 충돌 확인
- 병합 시 충돌이 발생하면 Git이 알려준다.
```
sharick@MacBook-Air duckmelang-backend % git merge --no-ff feature/#1
Auto-merging src/main/java/umc/duckmelang/domain/member/domain/Member.java
CONFLICT (add/add): Merge conflict in src/main/java/umc/duckmelang/domain/member/domain/Member.java
Auto-merging src/main/java/umc/duckmelang/domain/postidol/domain/PostIdol.java
CONFLICT (add/add): Merge conflict in src/main/java/umc/duckmelang/domain/postidol/domain/PostIdol.java
Automatic merge failed; fix conflicts and then commit the result.
```

- 충돌 파일 목록은 `git status` 명령어로 확인할 수 있다:
  ```bash
  $ git status
  ```
결과:
```
Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both added:      src/main/java/umc/duckmelang/domain/member/domain/Member.java
        both added:      src/main/java/umc/duckmelang/domain/postidol/domain/PostIdol.java
```


#### 2. 충돌 파일 열기
충돌 파일을 열어 Git이 추가한 마커를 확인한다:
```text
<<<<<<< HEAD
// 현재 브랜치의 변경 사항
=======
 // 병합할 브랜치의 변경 사항
>>>>>>> [브랜치 이름]
```

- `<<<<<<< HEAD`: 현재 브랜치의 변경 사항.
- ======= :  두 변경 사항의 경계.
- `>>>>>>> [브랜치 이름]`: 병합 대상 브랜치의 변경 사항.


### 3. 충돌 해결
충돌 파일에서 원하는 내용을 선택하거나, 두 변경 사항을 조합하여 수동으로 수정한다.

충돌 전:
```text
<<<<<<< HEAD
This is content from the current branch.
=======
This is content from the merging branch.
>>>>>>> feature-branch
```

수정 후:
```text
This is the combined content from both branches.
```
 

### 4. 충돌 해결 후 저장
수정된 파일에서 충돌 마커(<<<<<<<, ======= , >>>>>>>)를 제거한 뒤 저장한다.

### 5. 수정된 파일 스테이징
수정한 파일을 Git에 추가한다.
```bash
$ git add file.txt
```

### 6. 병합 커밋 생성
충돌 해결 후 병합을 완료하려면 커밋을 생성한다. 완료되면 푸시한다.
```bash
$ git commit -m "Resolve merge conflicts in file.txt"
```

### 7. 병합 중단(옵션)
만약 병합을 중단하고 이전 상태로 되돌리고 싶다면 아래 명령어를 사용한다.
```bash
$ git merge --abort
```
이 명령은 병합 시작 전 상태로 리셋한다.
