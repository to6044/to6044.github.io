---
layout: single
title: "3.version 관리"
categories: github
tag: [github]
---

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


3. detached HEAD 상태 해결 방법
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














