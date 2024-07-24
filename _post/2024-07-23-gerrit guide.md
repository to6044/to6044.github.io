---
layout: single
title: "1.gerrit guide"
categories: open source
tag: [gerrit]
---



## 계정 설정
- tutorial 참고


## local 개발 환경 설정
1. `sudo apt-get install git`

2. `sudo apt-get install git-review`

3. commit user 정보 global config
    - `git config --global user.name <name>`
    - `git config --global user.email  test@gmail.com"`

4. global config 확인
    - `git config --global --list`



## gerrit에 review 해보기
1. code 수정할 repository 다운로드
    - gerrit - 상단의 browse - repository - `aiml-fw/aimlfw-dep`


2. 해당 repo 안에서 작업


3. git init과 같은 역할
    - `git review -s`
    - ssh://<username>@gerrit.o-ran-sc.org.... 내용이 나오면 성공


4. code 수정


5. code 수정 여부 확인
    - `git status` : 현재 작업 중인 repo의 상태


5. code 수정된 file만 staging 영역에 추가
    - `git add <file>`
    - `git add .` 지양


6. commit (1, 2번만 진행, 3번은 참고)
    1. `git commit -s`
        - `--signoff` : 커밋 메세지 끝에 Signed-off-by line 추가
        - Singed-off-by line만 생성됨, 저장 후 나가기

    2. `git commit --ammed`
        - Change-Id 생성됨
        - 아래 양식에 따라 commit 요약, Issue-ID 작성


    0. `git commit -s --amend`
        - 본인 commit을 생성하지 않은 상태에서 이 command를 진행하게 되면 다른 committer의 commit을 변경할 수 있으므로 유의

    3. commit message 양식 

            <commit 요약>

            Issue-ID: AIMLFW-110

            Change-Id: Ie8e732be72ad629be41e5a.....
            Signed-off-by: <username> <user email>

        - 첫 글자는 무조건 대문자
        - Issue ID 추가
        - Change-Id 중복 피하기
        - gerrit 홈페이지 - 상단 changes - merged - 해당 subject 참고


34. review 전 commit message/code내용 최종확인 및 author 본인 계정 확인
    - `git log -p`


8. review
    - `git review`



## 추가 git command
- `git log` : commit history list

- `git log -p` : code 변경 내용을 포함한 commit history list

- `git show` : 가장 최근 commit의 상세 정보

- `git show <commit>` : 특정 commit의 상세 정보

- `git status` : 현재 작업 중인 repo의 상태
