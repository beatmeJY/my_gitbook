# Git

깃 브런치충돌 시 리베이스 머지로 해결하기.

*   now에서 작업 중 other-branch와 병합해야 할 때. (other-branch가 now-branch보다 먼저 끝날 때)

    * git stash
    * git switch -C other-branch
    * git pull origin other-branch
    * git switch now-branch
    * git rebase other-branch
    * git merge other-branch

