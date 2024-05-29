---
layout: single
title: "TASK vs ROLE"
categories: aether-onramp
tag: [SDRAN PROJECT]
---

### task
- 가장 기본적인 실행 단위
- playbook 내에서 직접 정의되거나 role 안에 포함될 수 있음

### role
- task 뿐만아니라 관련된 여러 작업을 묶어 재사용 가능한 단위로 구성됨
- 관련된 여러 tasks, vars, files, 등의 자원을 포함할 수 있음

- role directory의 예제
```bash
roles/
  my_role/
    tasks/
      main.yml      # 이곳에 Role의 Task를 정의
    handlers/
      main.yml      # 특정 이벤트나 조건이 발생했을 때 실행되는 특수한 task
                    # ex) 설정 파일이 변경 되었을 때 서비스를 재시작하는 작업
                    # 보통 playbook의 맨 마지막에 한 번만 실행됨
    templates/
      template.j2   # Jinja2 템플릿 파일
                    # 동적으로 데이터를 반영하여 설정 파일이나 script를 생성
    files/
      example.txt   # 복사할 파일
    vars/
      main.yml      # 변수를 정의
    defaults/
      main.yml      # 기본 변수
    meta/
      main.yml      # Role의 metadata를 정의하는 파일들을 포함
                    # role 의존성, platform 호환성, version 등 포함
```