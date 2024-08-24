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
