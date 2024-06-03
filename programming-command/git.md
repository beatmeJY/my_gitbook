# Git

깃 브런치충돌 시 리베이스 머지로 해결하기.

*   now에서 작업 중 new-branch와 병합해야 할 때. (new-branch가 now보다 먼저 끝날 때)

    * git stash
    * git switch -C new-branch
    * git pull origin new-branch
    * git switch now
    * git rebase new-branch
    * git merge new-branch

