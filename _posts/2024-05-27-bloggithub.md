---
layout: single
title: "Github command"
categories: github
tag: [github]
---


1. vs code에서 blog 수정 완료

2. 현재 branch 확인
```bash
git branch
```

3. (필요시) 새로운 branch 생성 + 이동
```bash
git checkout -b <branch_name>
```

4. 브랜치 이동
```bash
git checkout <branch_name>
```

5. 로컬 repository 최신화
- pull: 로컬 <- 원격

    ```bash 
    git pull origin main
    git pull <로컬 repository의 최신화 할 현재 branch> <다운받고 싶은 원격 repository의 branch 이름>
    ``` 

6. add :
```bash
git checkout <branch_name>
```

7. commit : 
```bash
git commit -m <"commit message">
```

8. 원격 repository로 수정사항 보내기
- push: 원격 -> 로컬

    ```bash 
    git push origin main
    git pull <로컬 repository의 수정 완료된 현재 branch> <최신화하고 싶은 원격 repository의 branch 이름>
    ```
보통 수정사항은 main으로 업데이트 하지 않고 따로 생성한 branch에 업데이트한다.

