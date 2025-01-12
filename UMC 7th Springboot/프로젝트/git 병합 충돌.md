
```
sharick@MacBook-Air duckmelang-backend % git merge --no-ff feature/#1
Auto-merging src/main/java/umc/duckmelang/domain/member/domain/Member.java
CONFLICT (add/add): Merge conflict in src/main/java/umc/duckmelang/domain/member/domain/Member.java
Auto-merging src/main/java/umc/duckmelang/domain/postidol/domain/PostIdol.java
CONFLICT (add/add): Merge conflict in src/main/java/umc/duckmelang/domain/postidol/domain/PostIdol.java
Automatic merge failed; fix conflicts and then commit the result.

```


```
Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both added:      src/main/java/umc/duckmelang/domain/member/domain/Member.java
        both added:      src/main/java/umc/duckmelang/domain/postidol/domain/PostIdol.java

```

충돌 상황에서는 Git이 충돌 파일에 특정 마커를 추가하며, 이를 수정하고 병합을 완료하면 된다.

1. 병합 충돌 확인
- 병합 시 충돌이 발생하면 Git이 알려준다.
  ```bash
  Auto-merging file.txt
  CONFLICT (content): Merge conflict in file.txt
  Automatic merge failed; fix conflicts and then commit the result.
  ```
- 충돌 파일 목록은 `git status` 명령어로 확인할 수 있습니다:
  ```bash
  $ git status
  ```
  출력 예:
  ```text
  Unmerged paths:
    both modified: file.txt
  ```

---

## **2. 충돌 파일 열기**
충돌 파일을 열어 Git이 추가한 마커를 확인합니다:
```text
<<<<<<< HEAD
// 현재 브랜치의 변경 사항
=======
 // 병합할 브랜치의 변경 사항
>>>>>>> [브랜치 이름]
```

- `<<<<<<< HEAD`: 현재 브랜치의 변경 사항.
- `=======`: 두 변경 사항의 경계.
- `>>>>>>> [브랜치 이름]`: 병합 대상 브랜치의 변경 사항.

---

## **3. 충돌 해결**
충돌 파일에서 원하는 내용을 선택하거나, 두 변경 사항을 조합하여 수동으로 수정합니다.

### **예시**
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

---

## **4. 충돌 해결 후 저장**
수정된 파일에서 충돌 마커(`<<<<<<<`, `=======`, `>>>>>>>`)를 제거한 뒤 저장합니다.

---

## **5. 수정된 파일 스테이징**
수정한 파일을 Git에 추가합니다:
```bash
$ git add file.txt
```

---

## **6. 병합 커밋 생성**
충돌 해결 후 병합을 완료하려면 커밋을 생성합니다:
```bash
$ git commit -m "Resolve merge conflicts in file.txt"
```

---

## **7. 병합 중단(옵션)**
만약 병합을 중단하고 이전 상태로 되돌리고 싶다면:
```bash
$ git merge --abort
```
이 명령은 병합 시작 전 상태로 리셋합니다.

---

## **도구 활용**
IDE 또는 Git 도구를 사용하면 충돌 해결이 더 쉬워질 수 있습니다:
- **`git mergetool`**: GUI 기반 도구를 실행하여 충돌을 시각적으로 해결.
- **Visual Studio, IntelliJ, VS Code** 등 IDE에서 내장된 병합 도구 사용.

---

## 요약 명령어 흐름
1. 충돌 확인: `git status`
2. 파일 열기 및 수정: 텍스트 에디터/IDE 사용.
3. 수정된 파일 스테이징: `git add [파일명]`
4. 커밋 생성: `git commit -m "Resolve merge conflicts"`

이 과정을 통해 병합 충돌을 해결하고 프로젝트를 정상적으로 진행할 수 있습니다!

출처
[1] 깃(Git) 충돌 시 해결 방법 - velog https://velog.io/@euisuk-chung/%EA%B9%83Git-%EC%B6%A9%EB%8F%8C-%EC%8B%9C-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EB%B2%95
[2] How To Resolve Merge Conflicts in Git - phoenixNAP https://phoenixnap.com/kb/how-to-resolve-merge-conflicts-in-git
[3] Visual Studio에서 병합 충돌 해결 - Microsoft Learn https://learn.microsoft.com/ko-kr/visualstudio/version-control/git-resolve-conflicts?view=vs-2022
[4] Git merge conflicts | Atlassian Git Tutorial https://www.atlassian.com/git/tutorials/using-branches/merge-conflicts
[5] 명령줄을 사용하여 병합 충돌 해결 - GitHub Docs https://docs.github.com/ko/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts/resolving-a-merge-conflict-using-the-command-line
[6] How to Resolve Merge Conflicts in Git? - Simplilearn.com https://medium.com/@Simplilearn/how-to-resolve-merge-conflicts-in-git-simplilearn-updated-1ea032eeb550
[7] GitHub에서 병합 충돌 해결 https://docs.github.com/ko/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts/resolving-a-merge-conflict-on-github
[8] How do I resolve merge conflicts in a Git repository? - Stack Overflow https://stackoverflow.com/questions/161813/how-do-i-resolve-merge-conflicts-in-a-git-repository/7589612
[9] How to resolve merge conflicts in Git - Graphite.dev https://graphite.dev/guides/how-to-resolve-merge-conflicts-in-git
