---
layout: single
title: "ansible.cfg"
categories: aether-onramp
tag: [SDRAN PROJECT]
---


```bash
cat ansible.cfg
[ssh_connection]
ssh_args = "-o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no -o ControlMaster=auto -o ControlPersist=5m -o LogLevel=QUIET -o ForwardAgent=yes"
control_path = /tmp/ansible-%%r@%%h:%%p

[defaults]
forks = 48
pipelining = True
host_key_checking = False
deprecation_warnings=False
hash_behaviour=merge                        
```



## `ansible.cfg`
- ansible의 구성 설정 파일

## [ssh_connection]
- SSH 연결 관련 설정

### `ssh_args`
- SSH 연결시 사용되는 추가 인자들을 정의

1. `UserKnownHostsFile=/dev/null`
- 일반적인 SSH의 첫 연결시, sever의 host key가 client의 'known_hosts'파일에 저장되고, 이를 참조하여 이후 연결에서 서버의 신뢰성을 검증함
- 위의 설정은 알려진 host file을 무시하고, host key 정보를 dev/null(아무것도 저장되지 않은 곳)으로 direction하여 모든 host key 정보를 무시함
- 자동화된 환경에서 서버에 대한 원활한 접근을 가능하게 하기 위함

2. `StrictHostKeyChecking=no`
- 원래 sever의 host key가 client의 'known_hosts'파일에 없을 경우에 경고를 발생시킴
- 이를 설정하면 위와 같은 검증 절차를 건너 뛰고 모든 host key를 자동으로 수락하게 됨

3. `ControlMaster=auto`
- 여러 SSH 연결을 위해 하나의 SSH process를 공유할 수 있게 함
- 여러 SSH 연결에서 하나의 마스터 연결을 사용하여 세션 공유를 가능하게 함
- auto로 설정되면, 자동으로 마스터 연결을 생성하고, 다른 연결들은 이 마스터 연결을 공유함, 이로 인해 연결과 인증 시간이 단축됨

4. `ControlPersist=5m`
- 마스터 연결이 명령어 실행 후에도 얼마나 지속될지 설정
- 초기 연결 후 추가적인 연결들이 빠르게 수행될 수 있게 함 (초기 연결 이후에 필요한 연결들을 연결이 끊어지기 전에 후다닥할 수 있도록 함)

5. `LogLevel=QUIET`
- 로그 수준을 낮춰 불필요한 로그 출력 줄임
- 로그 파일 줄이고 로그 가독성을 높임

6. `ForwardAgent=yes`
- SSH 에이전트 전달을 활성화
- local machine의 SSH key를 사용하여 원격 server에서 추가 SSH 연결을 초기화할 수 있게 함
- SSH 에이전트 : SSH key를 안전하게 저장하고 관리하는 프로그램, SSH session에서 비밀번호를 반복적으로 입력할 필요 없이 안전하게 인증할 수 있음

### `control_path = /tmp/ansible-%%r@%%h:%%p`
- SSH 연결의 소켓 파일 경로를 설정
- %%r : 원격 사용자 이름
- %%h : 호스트 이름
- %%p : 포트 번호

## [defaults]
- ansible의 기본동작에 대한 설정

### `forks = 48`
- ansible이 동시에 실행할 수 있는 최대 작업의 수
- 기본 설정은 5개
- 현재는 48개의 병렬작업이 가능함을 의미

### `pipelining = True`
- ansible이 SSH 연결을 통해 여러 명령을 일괄적으로 전송하게 하는 옵션
- 하나의 session을 사용하여 여러 작업을 수행
- 네트워크 지연시간 단축, 실행 속도 증가

### `host_key_checking = False`
- SSH 접속시 server의 host key가 client의 'known_hosts' 파일에 저장된 key가 일치하는지 확인하는 과정
- 이를 비활성화하고 모든 host key를 자동으로 수락


### `deprecation_warnings=False`
- 사용 중단된 기능에 대한 경고를 표시할지 여부 결정
- 중요한 경고만을 강조, 새로운 업데이터나 변경 사항을 놓칠 위험

### `hash_behaviour=merge`
- 동일한 이름의 변수가 여러 곳에서 정의될 때  이 변수 값들을 합치는 방식으로 처리함.
- 충돌 시 가장 최근의 값을 사용

