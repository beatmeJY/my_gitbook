# Git

### 깃 브런치 충돌 시 리베이스 머지로 해결하기.

now에서 작업 중 other-branch와 병합해야 할 때. (other-branch가 now-branch보다 먼저 끝날 때)

* git stash
* git switch -C other-branch
* git pull origin other-branch
* git switch now-branch
* git rebase other-branch
* git merge other-branch



### 깃 다른 브런치 메인에 merge 된 것 rebase로 내 브런치에 땡겨오기.

* git stash
* git switch main
* git pull origin main
* git switch "브랜치"
* git rebase main
* git stash pop

### 로컬 브랜치 변경 사항 깃에 강제 푸시

* git push -f origin branch&#x20;
