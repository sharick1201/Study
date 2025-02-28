첫 협업! git은 그저 기록용으로만 사용해왔는데, UMC에서 첫 팀프로젝트를 진행하면서 협업용으로도 사용하게 되었다. 이제껏 날치기로 써왔던 git 사용법을 정리하고자 문서 개설하였다.

---
#### 협업 규칙
##### 브랜치 전략
팀에서 선택한 브런치 전략은, Git Flow이다. master(main), develop, feature 브랜치를 주로 사용하게 된다. 기능 개발 시 feature 브랜치를 새로 파고, 작업이 완료되면 develop 브랜치로 병합(merge)한다. feature 브랜치는 각 기능 또는 작업 단위 별로 별도의 브랜치들을 생성하게 된다. master 브런치는 배포용 소스코드이다.
feature 브랜치명 형식은 `feature/#(이슈번호)` 로 팀 내에서 통일하였다.

##### 커밋 메세지 형식
제목, 본문, 꼬리말 세 파트로 나누었다. 굳이 본문을 넣은 이유는 해당 커밋의 의도, 즉 왜 했는지를 기술하는 것이 중요하다고 판단되었기 때문이다. 코드 변경의 의도를 꾸준히 고민하며 코드 작성 시 불필요한 코드를 줄일 수 있고, 스스로가 무엇을 하고 있는지 보다 명확하게 인지 가능하다.
```
태그: 제목
무엇을, 왜 했는지 간략하게 작성
이슈 유형: #이슈 번호
```
* 태그(첫 문자는 대문자로 한다.) 종류:
	* `feat` : 새로운 기능 추가
	- `fix` : 버그 수정
	- `build`: 빌드 관련 파일 수정 / 모듈 설치 또는 삭제
	- `ci`: CI 관련 설정 수정
	- `docs` : 문서 수정
	- `style` : 코드 포맷팅, 세미콜론 누락, 코드 스타일 변경
	- `refactor` : 코드 리펙토링
	- `test` : 테스트 코드, 리펙토링 테스트 코드 추가
	- `perf`: 성능 개선
	- `chore` : 그 외 자잘한 수정
* 이슈 유형 종류:
	* `Resolves` : 이슈 해결 시
    - `Fixes` : 이슈 수정 중 (아직 해결하지 않은 경우)

##### 커밋 주기
<u>최대한 잘게 쪼개어 최대한 자주 올리는 것</u>을 지향한다. (원자적 commit)
유의미하게 완성된 단위의 커밋보다, 작업한 결과물을 빠르게 공유하는 것이 더 중요하다. 피로감과 스트레스를 유발하는 충돌을 최소화해야하기 때문이다.

##### PR 규칙
main, develop 브랜치 모두 <u>2명</u>의 PR 승인을 요한다.

##### Issue
이슈도 함께 파서 진행한다. 이슈를 팔 때 이슈에 대한 설명, 작업할 내용(체크박스화), 참고 자료를 기술한다. assignees, label, development(연관 branch)도 함께 설정하여 진행한다. 작업이 완료되면 이슈를 닫는다.

#### clone
가장 처음 한 git 작업. 
팀원이 생성/초대해주신 Git Organization에서 백엔드 repository를 내 로컬로 clone해왔다.
어떻게? IntelliJ에 탑재되어 있는 저장소 복제 기능을 통해. clone하고 싶은 저장소 URL을 붙여넣기하고, '복제' 버튼을 누르면 쉽게 clone이 가능하다.



#### 브랜치
##### 현재 존재하는 브랜치 확인
```
git branch
```

##### 새 브랜치 생성
1. 새 브랜치의 시작점이 될 브랜치(기존에 존재하던 것)로 이동한다. switch 또는 checkout을 사용한다.(아래 두 명령어 중 하나만 사용하면 된다.)
```
git switch 기존브랜치명
git checkout 기존브랜치명
```
* switch
	* 현재 작업 브랜치를 다른 브랜치로 전환한다.
	* checkout의 역할이 많아(브랜치 전환, 파일 복원) checkout의 역할에 해당하는 명령어를 세부 분리하였다. 최신 git에서는 checkout보다 명령어의 역할이 보다 명확한 switch를 권장한다.
* checkout
	* 현재 작업 브랜치를 다른 브랜치로 전환한다. / 특정 커밋에서의 파일을 복원한다.
	* 최신 git에서는 checkout의 역할에 해당되는 다양한 기능을 분리하고, 각 역할에 해당하는 새로운 명령어들을 출시했다.
		* `git switch`: 브랜치 관련 작업(생성, 전환 등)에 집중.
		* `git restore`: 파일 복원 작업에 사용.

2. 새로운 브랜치를 생성한다. (마찬가지로 아래 두 명령어 중 하나만 사용하면 된다.)
```
git branch 새브랜치명        # 생성만
git checkout -b 새브런치명   # 생성과 동시에 새 브런치로 이동
git switch -c 새브런치명     # 생성과 동시에 새 브런치로 이동
```
* branch
* checkout의 -b 옵션
	* git branch 새브랜치명 -> git checkout 새브랜치명  두 단계로 나눠서 수행해야 했던 것을 하나의 명령어로 수행할 수 있도록 해주는 옵션. 새로운 브랜치를 생성하고 동시에 해당 브랜치로 전환(체크아웃)하는 데 사용된다.
* switch의 -c 옵션
	* checkout -b 명령어와 동일한 기능을 수행한다.
	* 최신 git에서는 switch 사용이 보다 권장된다.(명령어의 명확성을 위하여)

##### merge
병합하고자 하는 브랜치(ex. feature/#1)를 기준 브랜치(ex. develop)에 병합한다고 하자.
1. 기준 브랜치로 이동한다. (기준 브랜치가 최신 상태인지 확인할 것! pull을 하고 시작하자)
```
git switch develop
```

2. 병합한다.
```
git merge --no-ff feature/#1
```
* `--no-ff` 옵션
	* Git에서 병합(Merge)을 수행할 때 “non-fast-forward” 병합을 강제하는 옵션이다. 이 옵션을 사용하면 병합 대상 브랜치와 현재 브랜치가 fast-forward 관계에 있더라도, 반드시 새로운 병합 커밋(Merge Commit)을 생성한다.
	* 병합하려는 브랜치의 커밋 히스토리가 기준 브랜치의 커밋 히스토리를 포함하고 있을 때 Fast-Forward 관계가 성립한다.
		* <span style="background:rgba(240, 107, 5, 0.2)">음... 추가 공부 필요</span>
##### merge시 충돌 해결
* [[Git 병합 충돌]]


#### 커밋
##### 커밋하기 + 커밋 메세지 작성
1. 변경된 파일들을 확인
```
git status
```

2. 변경된 파일들을 스테이징
```
git add <파일명>      # <파일명>만 스테이징
git add .           # 변경된 파일을 모두 스테이징
```

3. 커밋 + 합의된 커밋 메세지 구조에 따라 커밋 메세지를 작성
```
git commit -m "커밋 메시지 작성"

git commit -m "이렇게
따옴표를
뒤에
붙이지 않으면

줄을 나누어 작성할 수 있다.
끝 따옴표가 작성되면 커밋된다"
```
 * -m 옵션
	 * 커밋 메세지를 작성하게 해준다.

##### 커밋 메세지 수정
* <u>마지막 커밋 메세지를 수정할 경우</u>
```
git commit --amend -m "수정된 커밋 메시지"
```
* 마지막 커밋 자체를 수정하는 것이기 때문에, 이미 원격 저장소에 푸시한 커밋이라면 주의 필요

* <u>마지막 메세지 그 이전의 커밋 메세지들을 수정할 경우</u>
	* 아래 명령어 실행 시 기본 텍스트 에디터가 나온다. 작성한 n개만큼의 커밋 목록이 나열된다.
	* 각 커밋 앞에는 pick이라는 키워드가 있다. `i`를 눌러 원하는 대로 수정하면 된다. 완료하면 `esc`키 + `:wq` 로 텍스트 에디터를 종료한다.
		- `pick`: 해당 커밋을 그대로 유지
		- `reword`: 커밋 메시지를 수정(메세지만 수정)
			- 에디터 종료 시 메세지를 수정할 수 있는 추가 에디터 등장
		- `edit`: 커밋 내용을 수정할 수 있는 상태로 전환(메세지+커밋의 파일 내용 변경 가능)
		- `squash`: 이전 커밋과 병합
			- 에디터 종료 시 두 커밋의 메세지를 병합하는 화면이 등장
		- `drop`: 해당 커밋을 삭제
```
git rebase -i HEAD~n
```
- Rebase:
	- 브랜치의 기반을 변경하여 커밋 기록을 재구성한다.
	- 커밋의 순서를 변경하거나, 커밋 메시지를 수정하고, 불필요한 커밋을 삭제할 수 있다.
 * HEAD:
	 * 현재 체크아웃된 커밋을 지시한다.
	 * `HEAD~n`은 현재 커밋에서 n개의 이전 커밋을 의미한다.
		 * `HEAD~1`: 직전 커밋
* `-i`
	* 인터랙티브 모드로 리베이스를 실행한다.
	* 커밋을 선택적으로 수정할 수 있다.

* <u>이미 푸시한 커밋을 수정한 경우</u>
	* 강제로 푸시해야 한다.
```
git push origin <브랜치명> --force
```
* 다른 팀원이 이미 해당 커밋을 기반으로 작업하고 있을 수 있으므로, 커밋 메시지 수정 시에는 팀원에게 알리자.



#### push
1. 현재 브랜치 확인
2. 원격 저장소 연결 확인
3. 로컬 브랜치를 원격 저장소에 푸시
```
git push -u origin feature/#2
```
* -u 옵션
	* `feature/#2` 브랜치를 원격 저장소의 `feature/#2` 브랜치와 연결(tracking)한다. 이후에는 `git push`만 입력해도 푸시할 수 있다.

4. 푸시 성공 메세지 확인
```
branch 'feature/#2' set up to track 'origin/feature/#2'.
```

#### pull
원격 저장소에서 파일을 내려받을 수 있다.
원격 저장소와 로컬 저장소의 상태가 동일해지기 위해서 사용한다.
```
git pull origin <브랜치명>
```




#### 리베이스(Rebase)
1. **feature/#49 브랜치로 체크아웃**:
```
git checkout feature/#49
```
    
2. **develop 브랜치에서 최신 변경 사항 가져오기**:
```
git fetch origin
```
    
3. **develop 브랜치 리베이스**:
```
git rebase origin/develop
```
    
4. 충돌 해결 (필요할 경우):
- 충돌이 발생하면, 충돌이 난 파일을 수정하고, 수정한 파일을 추가한다.
```
git add <conflicted-file>
```
    
- 리베이스 계속:
```
git rebase --continue
```

---
참고자료:
* https://inpa.tistory.com/entry/GIT-⚡%EF%B8%8F-깃헙-원격-저장소-관리-git-remote