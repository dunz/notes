# Git Cheat Sheet

## Config

##### 사용자 설정
```sh
// get
git config --global user.name
git config --global user.email

// set
git config --global user.name djlee
git config --global user.email djlee@test.com

git commit --amend --reset-autor // 사용자 설정후 기존 commit 반영

git config -e               // 로컬설정 vim으로 보기
git config --global -e      // 글로벌설정 vim로 보기
git config --list           // 설정 리스트 형식으로 보기
git config --list|grep core // core 설정만 보게끔 필터링
```

##### 글로벌 설정
> default는 개인계정, 그리고 해당 폴더 하위프로젝트에서는 회사계정으로 자동 설정 해놓기

`~/.gitconfig`
```
[user]
	name = dunz
	email = zergcake@gmail.com
[includeIf "gitdir:~/Works/"]
	path = .gitconfig-work
```
`~/.gitconfig-work`
```
[user]
	name = djlee
	email = djlee@smilegate.com
```

##### 인코딩 설정
```sh
gui.encoding				// utf-8
i18n.commitEncoding			// utf-8/cp949
core.quotepaht				// false
i18n.logOutputEncoding 		// cp949
```

##### git diff 설정
```sh
core.pager	                // cat   (default: '')
```
> cat으로 설정시 diff 모든 내용 한번에 출력

## Log
```sh
git log -p //변경사항까지 확인
git log --branches  --decorate --graph --oneline // 모든 브랜치의 로그 합쳐보기
git log [branch1]..[branch2]  // branch1에 없고 branch2에만 있는 로그 보기
git reflog  // reference log 보기
```

## diff
#####특정 commit간의 차이 확인
```sh
git diff [commitID1]..[commitID2] +++ 기준이 commitID2로 비교
git diff [branch1]..[branch2]   +++기준이 branch2로 비교
```

## Show
```sh
git show --name-only [commitID] // 해당커밋에서 수정된 파일들 보기
```

## 리모트

##### 리모트 추가
```sh
git remote add [remotename] [remoteurl]
```

##### push 리모트만 추가
```sh
git remote set-url [remotename] --add [remoteurl]
```

##### push 리모트 Disabled 처리
```sh
git remote set-url --push [remotename] [alias] ("DISABLE")  // URL 을 임의의 alias 로 바꿔주는 원리
```

## 브랜치

##### 로컬 브랜치명 수동설정
```sh
git checkout -b [branch] [remotename]/[branch]
```

##### 로컬 브랜치명 자동설정

```sh
git checkout --b [branch] [remotename]/[branch]
git checkout --track [remotename]/[branch]
```

#### 현재 브랜치를 리모트에 있는 해당 브랜치로 추적
```sh
git branch -u [remotename]/[branch]
```

##### 모든 브랜치들 및 그 브랜치들의 업스트림과 마지막 커밋 나열하기
```
git branch -vv
```

##### 특정 브랜치만 clone
```sh
git clone [remoteurl] -b [branch] [dirname]
```

##### Branch 이름 변경
```sh
git branch -m [change-new-branch]                  
git branch -m [change-old-branch] [change-new-branch]
```

##### 리모트 브랜치 받기
```sh
git fetch [remotename]
git checkout -b [branchname]
```

## Reset
```sh
git reset [commit id] --hard // 특정커밋으로 되돌리기 --hard 특정 커밋 이후 수정된파일 모두 사라짐
git reset --hard ORIG_HEAD // reset 취소
```
> reset은 말그대로 이전 커밋으로 돌아가는 것이기 때문에 공유되지않은 local환경에서만 활용해야한다
이미 공유된 commit에서는 이전 커밋으로 돌아가도 그것을 이미 공유받은사람이 다시 push할 경우 되살아나기때문에
이미 공유된 commit에서는 이전 커밋으로 돌아가는 reset이 아닌 이전 commit으로 하나의 commit을 새로 생성하는 revert를 활용해야 한다.

## Git Flow

##### git flow install
```sh
curl -L -O https://raw.github.com/nvie/gitflow/develop/contrib/gitflow-installer.sh
sudo bash gitflow-installer.sh
```

##### feature추가시 base branch 지정
```sh
git flow feature start [add-branch] [base-branch]
```

```sh
git flow feature finish [add-branch]
```

##### release 따서 출시 branch 생성
```sh
git flow release start [add-branch] [base-branch] // default base: develop
git flow release finish [add-branch]
git flow release publish [add-branch] 다른사람들에게 게시
```

##### hotfix 따서 branch 생성
```sh
git flow hotfix start [hotfix-version] [base-branch] // default base: master
git flow hotfix finish [hotfix-version]
git flow hotfix publish [hotfix-version]
```

##### git reset
```sh
git reset --hard
// pull 이전으로 되돌리기
```

## Core
```
// autocrlf 해제
git config core.autocrlf false
```

## 태그
```
// 로컬 태그 삭제
git tag -d [태그]
git tag -a [태그] -m "annotation"   // annotated tag 생성

// 원격 태그 삭제
git push [저장소명] :[태그]
// 원격 태그 일괄 삭제
git tag -l | grep <정규식>  | xargs git push --delete <remote name>
예>>>>>   git tag -l | grep 20180[8-9].* | xargs git push --delete upstream
// 원격 태그 가져와서 로컬 참조 만들기
git fetch origin refs/tags/[:tag]]:refs/tags/[:tag]]
// 원격 태그 모두 받기
git fetch [:remote] --tags
```

## Stash
```sh
    git stash
    git stash save [name]
    
    git stash apply
    git stash pop
```
> stash에 Untracked 파일은 stash에 포함되지 않는다.

## git flow 실험
release start 시 

    - base를 지정하지 않으면 현재 내가 위치한 branch와 상관없이 default로 develop를 기반으로 release 가 생성됨
    - base를 지정하면 설정한 branch를 기반으로 release가 생성되고 base branch로 relese finish 할때 머지됨

release start 한 후 base가 되었던 branch를 지울운 후 release finish할 경우 에러발생

`The base 'feature/test' doesn't exists locally or is not a branch. Can't finish the release branch 'release/test2'.`

## 충돌
```baash
// 새로운 폴더로 기존 저장소 덮어쓰기 
git push // 혹은
git pull // 시에 에러가 나게 된다
fatal: refusing to merge unrelated histories  // 관련없는 히스토리의 병합이 거부되는 것인데

git pull origin master --allow-unrelated-histories // 이 옵션을 주게 되면 충돌이 나면서 merge할수있도록 pull받아진다.
// 충돌을 해결 한후 push 하면 되는 것이다.
```

## Merge, Rebase, Squash merge 비교
### 상황
1. `master`에서 `commit 1`, `commit 2` 커밋
2. `master`에서 `issue` 브랜치 생성
3. `issue`에서 `commit 3`, `commit 4` 커밋
4. `master`에서 `commit 5` 커밋 (`commit 3`의 수정 부분과 `commit 5`는 수정 라인이 같음, `README.md`충돌상황)

#### Merge
1. `master`에서 `issue` 브랜치 merge
> master$> `git merge issue`
2. 완료 > 커밋이 생성된 시간 기준으로 중간에 삽입되고 충돌병합 커밋이 추가된다.
```sh
0edf021 (HEAD -> master) resolve conflict
fc9507c commit 5
05a4118 (issue) commit 4
e8759fc commit 3
170958f commit 2
df55c22 commit 1
```
##### 장점
- 충돌병합을 한번에 끝낼 수 있다.

##### 단점
- 시간순으로 머지된 커밋이 중간에 삽입되기 때문에 merge 이전 커밋들만 찾기 어려워 되돌리기 힘들다.
- resolve conflict 커밋이 추가로 생성된다.


#### Rebase
1. rebase 할 브랜치로 이동하여 `rebase`실행
> issue$> `git rebase master`
2. `commit 3` 머지 과정에서 충돌 해결후 git add (별도의 rebase용 브랜치에서 일련의 작업이 실행됨)
> fc9507cR> `git add README.md` or `git add .`
3. rebase 계속 진행
> fc9507cR>  `git rebase --continue`
2. `commit 4` 머지 과정에서 충돌 해결후 git add (별도의 rebase용 브랜치에서 일련의 작업이 실행됨)
> fc9507cR> `git add README.md` or `git add .`
4. rebase 계속 진행
> fc9507cR>  `git rebase --continue`
5. 완료 > `commit 3`과 `commit 4`가 `commit 5`보다 먼저 생성되었지만 `master`에서 `commit 5`커밋이 생성된 이후에 `commit 3`과 `commit 4`가 생성된 것으로 재정렬 되며 resolve conflict 머지는 추가로 생기지 않는다.
```sh
bac3d58 (HEAD -> issue) commit 4
7eadde3 commit 3
fc9507c (master) commit 5
170958f commit 2
df55c22 commit 1
```
##### 장점
- rebase대상이 되는 `master`의 커밋 위로 재정렬 되기 때문에 merge 이전 커밋으로 되돌리기 쉽다.
- 충돌 병합 커밋이 추가로 생성되지 않는다.

##### 단점
- 충돌이 많을 경우 여러번에 걸쳐서 연속적으로 병합작업을 해야해서 번거롭다.



