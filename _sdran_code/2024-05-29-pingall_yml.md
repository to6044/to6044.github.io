---
layout: single
title: "5.pingall.yml"
categories: aether-onramp
tag: [SDRAN PROJECT]
---

```bash
---
- name: ping all hosts
  hosts: all
  gather_facts: true

  tasks:
  - name: ping all
    ping:         
```

- `---` : yaml 파일의 시작
- `name` : playbook의 이름을 설정

- `ping:` : pingall이라는 이름으로 실행되는 tastk로 네트워크 핑 테스트가 아니라 ansible 관리 노드와 대상 host 간의 ansible 연결을 확인함

