---
layout: single
title: "10.EC2(EBS,Snapshot,AMI)"
categories: AWS
tag: [cloud]
---


## EC2의 구성
1. 컴퓨터의 구성
    - 컴퓨팅
    - 하드디스크
    - 랜카드

2. EC2의 구성
    - EBS (하드디스크) -> 이거 소개하는 파트
    - Snapshot
    - AMI


## EBS (Elastic Block Storage) 소개

1. Amazon EC2 instance에 적용할 "영구 + block storage"

2. 가상의 하드 드라이브

3. EC2와 EBS가 network로 연결되있음
    - network 연결로 효율적인 EBS 운용이 가능
        - EC2 instance가 종료되도 저장내용이 유지 
        - EBS입장에서, instance up/downgrade 용이 (다른 isntance에 network만 변경하면 됨)
        - instance입장에서, EBS 추가/제거 용이 (instance에 EBS를 network로 쉽게 연결)
        - istance가 필요없는 상황이면 종료해도 EBS는 저장내용이 유지되기 때문에, 요금을 절약할 수 있음
        - 1개의 EBS를 다수의 EC2 장착 가능함 (EBS Multi Attach)

4. root volume으로 사용시 EC2 종료 시 같이 삭제됨.
    - 단, 설정을 통해 EBS만 따로 존속 가능

5. EC2와 같은 가용 영역에 존재함!!

6. type
    <img  src="/assets/posts/aws/11.png" alt=""/>





## Snapshot
- 특정 시간에 EBS의 저장본
- 필요시 snapshot을 통해 특정 시간의 EBS를 복구 가능
- S3(파일 저장 service, 가격 쌈)에 보관 (증분식 저장)
    - 증분식 : 변화 결과 저장 x, 변화한 것만 저장하여 총 저장 용량을 줄일 수 있음


## AMI
- Amazon Machine Image
1. 정의
    - EC2 instance를 실행하기 위해 필요한 정보를 모은 단위
        - OS, arachitecture type, 저장공간 용량 등...
2. 기능
    - snapshot을 기반으로 AMI 구성 가능 
    - AMI를 사용하여 EC2를 복제하거나 다른 region으로 전달 할 수 있음
    
3. 구성
    - 1개 이상의 EBS snapshot
    - instance storage instance인 경우 :  root volume에 대한 템플릿 (OS, application server, application)
    - 사용 가능한 AWS account
    - block device mapping (EC2 instance에 몇 개의 EBS가 붙는가)
    - type
        1. EBS 기반
            - EC2, EBS : network 연결
            - snapshot을 기반으로 root device 생성
        
        2. instance storage 기반
            - EC2 내부에 EBS 존재
            - 장 : 속도가 빠름
            - 단 : instance storage 삭제시, EBS도 삭제됨 (영구적 EBS x)
            - S3에 저장된 template를 기반으로 생성


4. 생성과정
    1. EBS snapshot (OS, file, auth)
    2. snapshot을 S3에 저장
    3. AMI 등록
    4. EC2 실행 or EC2 복사













