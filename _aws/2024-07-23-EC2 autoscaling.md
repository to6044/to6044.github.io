---
layout: single
title: "12.EC2_autoscaling"
categories: AWS
tag: [cloud]
---


## EC2를 잘 활용하기 위한 service들을 공부할 예정


## Scaling
- scaling : instance (computing power)를 늘리는 것

1. Vertical Scale (Scale up)
    - instance의 성능을 향상시킴
    - 성능에 비해 비용이 기하급수적으로 증가

2. Horizontal Scale (Scale Out)
    - 성능향상이 아닌 규모를 늘림
    - 성능과 비용이 정비례

3. 즉, cloud 환경에서는 Scale up이 아닌 Scale Out을 고려해야함
    - 비싼 걸 사용 하는 것이 아닌 저렴한 instance의 규모를 줄이거나 늘려서 유연하게 대처



## AWS Auto Scaling
- cloud의 Horizontal Scale을 지원하기 위한 service
- 자동으로 용량을 조정하여 최대한 저렴한 비용으로 안정적이고 예측 가능한 성능을 유지.
- EC2, DDB, Spot Fleet, Aurora, ECS Auto Scaling


## EC2 Auto Scaling

1. 목표
    - 정확한 수의 EC2 instance를 보유하도록 보장
        - group의 instance의 min과 max 수를 선택할 수 있음
        - 다양한 scaling 정책 적용 가능
            - CPU 부하에 따른 instance 크기 변경

    - 가용 영역에 instance가 골고루 분산될 수 있도록 instance를 분배
        - 가용 영역이 고장났을 때 완전 down되는 것을 방지
    


2. 구성
    - launch template (**무엇**을 실행시킬 것인가?)
        - EC2 type, size
        - AMI
        - 보안 그룹, key, IMA
        - user data
    
    - monitoring (**언제** 실행 시킬 것인가?)
        - +상태확인
        - 어떤 상황에서 auto scailing을 실행할 것인지
        - CloudWatch(monitoring)와 ELB(load balancing)와 연계됨

    - setting (**얼마나 어떻게** 실행 시킬 것인가?)
        - max/min instance
        - ELB와의 연동 등



3. 동작 방식
    1. instance 삭제
    2. monitoring
    3. launch template에 맞는 instance 생성





## 실습

1. instance에서 시작 템플릿 (launch template)
    - auto scaling에서 어떤 istance를 생성할지 결정

    1. 이름, 설명, 도움이 되는 지침 제공 체크
    2. AMI, instance type - free tier로 설정
    3. keypair 설정 x
    4. network 설정
        - VPC
        - 보안그룹 설정 x (network interface에서 설정함)
    5. storage 설정 - default
    6. network interface (network 설정 고급에 있음)
        - public IP 자동 할당 활성화
        - 보안그룹 defalut
        - 종료시 삭제 - 예
    
    7. 생성

2. auto scaling 그룹 생성
    1. 이름 작성
    2. 시작 템플릿 선택
    3. 다음
    4. VPC 두고
    5. 가용 영역 선택 (전체 ㄱ)
    6. 그룹 크기 (min/max) 다 2
    7. 나머진 다 스킵
    8. tag 생성시 instance가 생성될 때 tag가 붙음 (어떤 autoscaling의 instance인지 확인 가능)


3. 생성 후 확인
    - 활동에서 instance가 만들어 지는 것이 보임


4. 1개 instance 삭제해보고 활동 등 확인

5. 종료전에 항상 instance 요구량을 0으로 맞춰놔야 비용 발생하지 않음


     

