---
layout: single
title: "3.version 관리"
categories: github
tag: [github]
---


- 좋은 site (HEAD가 pointer임, branch, commit 흐름을 시각적으로 확인할 수 있는 tool)
- https://git-school.github.io/visualizing-git/
- `git status`를 습관화하자!



## version 관리 (10강)

1. git
    - version 관리 시스템
    - 실행취소/재실행이 가능
    - version간 비교가 용이
    - 협업이 수월

2. commit
    - 수동저장, 사용자가 의도를 가지고 코드를 저장하는 시점을 결정
    - git의 기본 단위
    - commit 하나는 독립적인 version을 나타냄. 
    - commit 크기는 작을 수록 좋음
    - commit은 논리적 변경이 없을 때 마다 생성
    - pr의 크기도 중요


## commit (11강)

1. repository
    - 여러 파일을 모아 놓은 컬렉션

2. `git log`
    - commit한 내용을 볼 수 있음
    - git graph에는 다시 정리 되어있음
    -`gitk`를 이용해도 볼 수 있음
    - `git log --stat` 변경된 line 수


## commit checkout (13강)

1. checkout 의미 : 점검, 검사
    - commit을 checkout한다
    - commit을 점검, 검사한다
    - 과거 시점으로 돌아가고 싶으면 commit을 checkout하면 된다

2. 이전 commit으로 돌아가는 방법
    - 체크아웃해서 특점 시점의 코드 상태를 살피기 위함
    - 롤백 프로세스 관련 스크립트 작성할 때
    - `git checkout <commit ID> `
        - **HEAD : 현재 checkout한 branch의 가장 최신 commit을 가리키는 포인터**
        - checkout 전 : HEAD = master
        - checkout 후 : detached HEAD 

3. detached HEAD란
    - HEAD가 branch로부터 떨어져 있는 상태
    - branch를 통해서가 아니라 다이렉트로 commit을 참조하고 있는 상태
    1. HEAD는 하나만 존재
    2. HEAD는 특정 branch를, branch는 특정 commit을 가리킴
    3. checkout 명령어로 특정 commit을 checkout할 경우에 참조하던 branch를 잃어버리고 직접 특정 commit을 참조하는 상태를 말함

4. detached HEAD 상태 해결 방법
    - 파일 수정, commit 등 다른 작업 지양
    - `git checkout main/master`를 이용해 detached 상태를 벗어난 후에 수정
    - commit하면 다른 명령어를 사용해서 찾아야함 (`git log`로 못 찾음)
    - `git reflog`를 통해 detached HEAD에서 만든 commit을 확인할 수 있음
    - `git reflog`는 HEAD인 pointer가 가르킨 commit을 나열하는 명령어
    - 실수로 파일 수정 시 파일 수정 내용 삭제 가능 : `git checkout -- <filename>`
    - 수정내용 임시저장 및 원본으로 복구 : `git stash`
    - 수정내용 복구 : `git stash pop`


## repository (16강)
- 컴퓨터 입장에서 repository도 directory 중 하나
- 대신 repository는 .git 파일이 있는 directory



## staging 영역, commit (18강)
- repository에서 내가 일부 directory만 저장하고 싶은 상황이 생김
- 내가 저장하고 싶은 일부 directory를 staging 영역에 올린 뒤에 commit해야한다
- commit은 논리적 변경이 생겼을 때 시행하고, 이 때 불필요한 파일들이 commit에 포함되지 않도록 관리할 수 있는 중간 관리 지대
- 확인 방법
    1. 파일 수정
    2. `git status`로 변경된 파일, not staged 확인
    3. `git add <file>`
    4. `git status`로 stage 됨을 확인
    5. 만약 un stage하고 싶으면 : `git reset HEAD <file>`

 - `git add .`
    - 다른 editor를 사용해서 이 파일을 열면 github와 관련없는 숨김 파일이 생성되는데, 이런 파일들은 staging 영역에 올라가면 안되기 때문에 조심해서 사용해야함
    - `.idea`등의 editor에서 개인 설정이 다른 사람의 작업환경에 영향을 줄 수 있음
    - 1번에 여러가지 파일을 staging 영역에 올리려면 번거롭기 때문에 GUI tool을 쓰는 것도 추천함


## gitignore 파일 (18강)
- OS나 쓰고있는 응용프로그램 혹은 framework, 언어에 따라 의도치 않게 저장소 내에 파일이 생성되거나 수정되는 경우
- commit에 포함시키지 않기 위해서 gitignore 파일을 만들어서 관리함
- git ignore generator이용할 수 있음


## branch (20강)
- commit을 가르키는 pointer
- 특정한 목표를 가지고 코드를 수정하기 시작할 때 만듦
- issue를 등록하고, issue에 맞는 branch를 생성하여 작업하는게 일반적

1. `git remote add origin <github repository주소>`
    - local repository랑 github repository를 연결하고, 이름을 origin으로 쓴다
2. `git branch -M main`
    - local repository의 이름을 master에서 main으로 변경
3. `git push origin main`
    - `git push <원격 저장소> <원격 저장소 branch>`
    - 현재 local branch를 push할 때 사용.
    - origin main은 github repository의 main branch에 push할 것임을 의미


1. branch 생성
    - `git branch <branch이름>`
2. branch 변경
    - `git checkout <branch이름>`
    - `git switch <branch이름>`
3. branch 생성 + 변경
    - `git checkout -b <branch이름>`



## merge (21강)
1. merge
    - A branch를 B branch에 merge
        1. `git checkout A` : A branch로 checkout(전환)한다.
        2. `git merge B`를 입력
    - pull request를 사용하기도 함


2. 충돌 나는 경우
- 어떤 코드를 남겨야할 지는 직접 판단
- 충돌 1
```bash
<<<<<<<HEAD
.....                         #HEAD 즉, 현재 checkout한 branch의 source code
===========
.....                         #병합하려고한 branch의 source code
>>>>>>병합하려는 branch이름
```

- 충돌 2
```bash
<<<<<<<HEAD
.....                           #HEAD 즉, 현재 checkout한 branch의 source code
|||||||merged common ancestors
.....                            #두 branch의 공통 조상의 source code
===========
.....                            #병합하려고한 branch의 source code
>>>>>>병합하려는 branch이름
```

- 충돌 해결 후에는 `git add`, `git commit`하여 충돌 수정 완료하면됨




## rebase (24강)
- 주둔지가 옮겨짐
- 만약 A branch에서 `git rebase B` 명령을 실행하면, A branch 정보를 복사하여 주둔지만 B branch로 옮김
- visualizing git으로 해보면서 이해해보기
<img  src="/assets/posts/github/4.png" alt=""/>
<img  src="/assets/posts/github/5.png" alt=""/>



1. merge vs rebase
    - merge
        - 대부분의 경우에 merge commit이 생김
    - rebase
        - merge commit이 생기지 않음
        - 일반 merge보다 git graph가 더 간단해져서 프로젝트 관리에 용이






## commit 되돌리기 (25강)
1. `git reset`
    - 현재 checkout한 branch의 원하는 과거 commit으로 HEAD를 이동시키고, 그 이후 commit을 버릴 수 있음
    - 원하지 않는 commit을 되돌릴 때 유용한 명령어
    - 주로 혼자 작업하는 repo에서 사용
    - `--soft`, `--mixed`, `--hard` flag를 붙여서 사용
    - `git resed HEAD~1` : HEAD를 기준으로 1개 commit만 삭제함
        - 삭제된 commit은 `git reflog`를 사용하면 복구 가능

2. `git revert <commit ID>`
    - 팀 repo에서 사용, reset과 조금 다름
    - 새로운 commit이 자동으로 생성됨











