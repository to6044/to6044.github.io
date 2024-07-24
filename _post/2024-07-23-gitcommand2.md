---
layout: single
title: "4.gitcommand with ossca"
categories: github
tag: [github]
---





1. `git reset HEAD~1`
    - HEAD를 1 commit 전으로 이동시켜 해당 commit을 삭제
    - 파일의 변경 내용은 작업 directory에서 삭제
        - `--soft`를 사용하면 파일의 변경사항 그대로 남음


2. `git checkout <path>`
    - <path>에 있는 file이나 directory를 이전 version으로 되돌림
    - commit이나 branch에서 가져옴


3. `git fetch origin`
    - 원격 저장소의 모든 브랜치와 태그를 로컬 저장소로 가져옴
    - 현재 작업 중인 로컬 브랜치에는 영향을 주지 않고 로컬 저장소를 업데이트함.


4. `git checkout origin/master`
    - 원격 저장소의 master branch를 체크아웃하여 로컬에서 해당 branch의 파일 상태를 확인
    - detached HEAD 상태로 변경


6. `git stash`
    - 현재 작업 중인 변경 사항을 임시 저장소에 저장
    - 작업 directory를 깨끗하게 하고, 변경 사항은 다시 꺼낼 수 있음


7. `git branch -D master`
    - local 저장소에서 master branch를 강제로 삭제


8. `git stash pop`
    - 마지막으로 저장한 stash를 적용하고, stash 목록에서 제거
    