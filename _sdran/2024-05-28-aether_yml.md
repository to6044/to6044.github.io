---
layout: single
title: "aether.yml"
categories: aether-onramp
tag: [SDRAN PROJECT]
---


```bash
cat aether.yml
---
- name: provision aether
  hosts: all
  gather_facts: true

  vars_files:
    - "vars/main.yml"

  roles:
    - aether                         
```



## `aether.yml`
- ansible playbook의 yaml 파일

## `name: provision aether`

## `hosts: all`
### `hosts`
- playbook이 실행될 호스트를 지정

### `all`
- inventory에 정의된 host에서 이 playbook을 실행한다는 의미

## `gather_facts: true`
- playbook이 실행될 때 호스트로부터 시스템에 관한 기본정보(os, network interface 등)을 수직하도록하는 옵션

## `vars_files:  - "vars/main.yml" `
- 변수파일의 경로가 지정됨
- vars/main.yml에 playbook에서 사용되는 변수가 저장되어 있음


## `roles:  - aether `
### `roles:`
- ansible playbook에서 재사용 가능한 작업을 모아놓은 집합
- role은 특정 기능을 구현하는데 필요한 작업, 템플릿, 변수 등을 포함함
- playbook을 모듈화하고 재사용성을 높일 수 있음
- ansible directory에 있을 것으로 예상됨
- tasks, handlers, vars, defaults 등의 구성요소를 포함


### `aether `
- 어딘가에 정의된 'aether'라는 role을 사용해서 특정 작업을 수행하게 될 것임을 의미