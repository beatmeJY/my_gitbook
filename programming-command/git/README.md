# Git

**깃 pull할때와 push할때 `carriage-return (\r)` 없애고 붙이는거 자동으로 해주는 설정**



윈도우 : git config --global core.autocrlf true\
리눅스 : git config --global core.autocrlf input

**VSC로 diff 변경 사항 VSCode로 확인 하기**



"git config --global -e" 해서 아래 코드 추가하기.

```
[diff]
    tool = vscode
[difftool "vscode"]
    cmd = code --wait --diff $LOCAL $REMOTE
```

**로그 이쁘게 확인하기**



아래 코드 추가한 후 "git history" 치면 로그 이쁘게 확인 가능. (history는 이름 임의로 변경 가능)

```
git config --global alias.history "log --graph --all --pretty=format:'%C(yellow)[%ad]%C(reset) %C(green)[%h]%C(reset) | %C(white)%s %C(bold red){{%an}}%C(reset) %C(blue)%d%C(reset)' --date=format:'%Y-%m-%d %H:%M:%S'"
```

**깃 merge 할때 VSCode로 작업하기.**



"git config --global -e" 해서 아래 코드 추가하기.

```
[merge]

    tool = vscode

[mergetool "vscode"]

    cmd = code --wait $MERGED
```

**깃 merge 할때 백업 파일 안만들기.**



"git config --global mergetool.keepBackup false"

## Git 공간



**working directory** : 작업 공간 untracked와 tracked로 나뉘고 새로 만든 파일은 untracked에 있음.\
**staging area** : add 시 들어가는 공간\
&#xNAN;**.git directory** : 커밋 시 들어가는 공간

## Git 명령어



git rm --cached : staging area에 있는 add 했던 파일 정보를 지움.\
git diff : 깃 변경 상세 내역 보기 (뒤에 " --stated" 추가하면 스테이지 내용도 확인가능.)\
git difftool : vs코드로 확인 하기 가능

git rm : 일반 rm 기능과 함께 git에도 삭제 변경 내용이 바로 적용.\
git mv : 일반 rm 기능과 함께 git에도 이름 변경한 변경 내용이 바로 적용.\
git log : 깃 로그 보기\
git log -p : 깃 커밋 로그에 변경파일 내용도 확인 가능.\
git log -3 : 최근 3개만 확인 가능.\
git log --oneline : 깃 커밋 로그에 해쉬코드 확인 가능. (" --reverse" 붙이면 오래된 것부터 정렬되어 볼 수 있음)\
git log --before="2023-07-17" : 230717 이전 커밋만 확인 가능.\
git log --grep="page" : "page"가 포함된 커밋메세지의 커밋만 확인 가능.\
git log -S "about" -p : 변경사항 중 "about" 이 들어있는 커밋만 확인 가능.\
git log -p about.txt : "about.txt" 해당 파일의 커밋을 확인 가능.

git show 9186a42 : 해당 "9186a41" 해시코드의 커밋 내용 확인 가능.\
git show 9186a42:repository.txt : 해당 "9186a41" 해시코드의 커밋 중 repository.txt 파일 내용만 확인 가능.\
git diff 9186a42 3ca14a1 : 두 해시코드의 커밋끼리 비교 가능.\
git tag v1.0.0 1563681 : 해당 "1563681" 해시 코드 커밋에 "v1.0.0"라는 tag를 만듬.\
git tag : 레파지토리에 있는 모든 tag를 확인 할 수 있음.\
git show v1.0.0 : "v1.0.0" tag 내용들 확인 가능.\
git tag -l "v1.`*`" : 태그 이름에 "v1"이 포함된 tag들 확인 가능.\
git tag -d v1.0.0 : "v1.0.0" tag 삭제 가능.\
git push origin v1.0.0 : 해당 "v1.0.0" tag를 서버와 싱크 함.\
git push origin --tags : 모든 tag를 서버와 싱크 함.\
git push origin --delete v1.0.0 : 해당 "v1.0.0" tag를 서버에 삭제 함.

git checkout "브런치이름 또는 tag 이름 또는 해시코드 등" : 해당 브런치, 태그 등으로 HEAD를 이동 가능 함.\
git checkout -b "TEST 브런치" "브런치이름 또는 tag 이름 또는 해시코드 등" : HEAD 이동하면서 "TEST 브런치" 이라는 브런치도 이동한 곳에 생성함.\
git switch -C new-branch : "new-branch"를 생성하고 스위치 함.

git branch -v : 브런치 목록 보기. git branch --merged : 머지 된 브런치 목록 보기 (생성만 되고 줄기 안나간 브런치도 master 아래 뜸) git branch --no-merged : 머지 안된 브런치 목록 보기 git branch -d branchName : 브런치 삭제하기. git branch --move fix welcome : 브런치 이름 "fix" -> "welcome" 으로 변경.

git push origin --delete new-branch : "new-branch" 브런치 원격저장소에도 삭제. git push --set-upstream origin welcome : "welcome" 브런치 원격저장소에 저장.

git merge --no-ff test1 : "test1" 브런치의 패스트포워드 머지를 막고 브런치가 새로 나가서 병합된거 처럼 보임. git merge test : "test" 와 마스터를 머지 함. git merge --abort : 머지를 취소 할 수 있음.

git clean -fd : 깃 디렉토리 정리.

git rebase : 브런치가 바라보고 있는 마스터의 포인터를 바꿀 수 있다. git rebase --onto master service ui :

git cherry-pick bbac9d1 : 가져오고 싶은 브런치의 부분 커밋만("bbac9d0") 마스터로 가져올 수 있음.

git stash push -m "test" : "test"라는 임시 보관이 생김. git stash push -m "test2" -keep-index : 작업하고 있던 파일들을 그대로 나둔 상태로 stath만 저장.

git patch :

git restore : 깃 스테이지에 있는걸 다시 가져온다. git restore --source=HEAD\~2 test.txt : "test.txt" 파일을 헤드부터 2번째 전의 커밋에 파일로 만든다. (없다면 삭제)

git commit --amend : 이미 커밋했던 커밋 파일내용과 커밋 요약메세지를 수정할 수 있다.

git reset --soft : 파일은 안 없애고 커밋내용 등을 초기화 할 수 있음. git reset --mixed : 스테이지 아리아 올리기 전으로 돌아감. git reset --hard HEAD\~2 : 로컬에서 사용하고 있는걸 깔끔하게 초기화 함.

git reflog : 여태 했던 작업기록내용 전부 볼 수 있음.

git revert C5 : 커밋된 C5을 빼고 감.

커밋 까먹을 때를 대비하여 git log git rebase -i {Hash Value} git commit --amend --allow-empty --no-edit --date="JAN 01 12:00:00 2024 +0000" git rebase --continue git push -f origin main







### 깃 브런치 충돌 시 리베이스 머지로 해결하기.

now에서 작업 중 other-branch와 병합해야 할 때. (other-branch가 now-branch보다 먼저 끝날 때)

* git stash
* git switch -C other-branch
* git pull origin other-branch
* git switch now-branch
* git rebase other-branch
* git merge other-branch



### 깃 다른 브런치 메인에 merge 된 것 rebase로 내 브런치에 땡겨오기.

```git
$ git stash
$ git switch dev 
$ git pull origin dev 
$ git switch ${BRANCH} 
$ git rebase dev 
$ git fetch origin ${BRANCH} 
$ git rebase origin/${BRANCH}
$ git stash pop
```



### 이전 브랜치 기반 다음 브랜치 생성 후 이전 브런치 작업 내용 squash merge 하기.

```git
git switch -C new-branch before-branch
git rebase -i origin/dev
```
