---
layout: single
title: "1.ransimulator"
categories: ransim
tag: [SDRAN PROJECT]
---

## Saurav Das의 introduction
- RANSIM의 역사, 내부 구조, 외부 시스템과의 상호작용
- 과거 RIC은 ORAN의 compliance를 따르지 않았음
- 최근(2020)에 RIC이 ORAN의 compliance를 따르게 되면서 RANSIM도 ORAN의 compliance를 준수해야함


## RANSIM이 emulate하는 요소
1. E2Node(eNB)
    - transmission power
    - directional antenna
    - 3-sector eNB (3 cells)
2. UE
    - mobility (random waypoint model을 사용)
3. E2Node <-> UE : radio signal
    - angle : directional antenna model
    - distance : Free-space path-loss model
    - SNR : static background noise (작업중)
4. Report message (Periodically)
    - gRPC protocol 이용
    - E2Node, UE에서 RIC으로 전송
5. 각 E2Node는 RIC의 xApp에 의해 제어
    - HO xApp : hanover request message를 E2Node에 보냄
    - MLB xApp : (서비스 범위 변경) transmission power를 조정하여 coverage를 줄이거나 늘리는 message를 node에 보냄

## RANSIM Internal Architecture

<img  src="/assets/posts/sdran/1.png" alt=""/>

1. E2Node
    - 각 E2Node는 단일 객체(single object)를 지님
    - 각 E2node가 가지고 있는 gRPC server : RIC core와 통신
    - E2node와 연결된 또 다른 gRPC server : onos-topo, onos-config가 modeled E2Node information을 전송함
        - modeled E2Node information : topology, E2 configuraiton information

2. UE
    - UE information : loation, sevice cell, neighbor cell

3. function of RANSIM
    - Metrics
        - end-to-end handover latency를 시각화하는 기능
    - UE Route
        - UE의 위치를 계산하는 기능 (앞 2번에서 UE의 mobility가 emulate 되기 때문)
    - Radio emulation
        - E2Node와 UE 거리를 고려하여 RSSI값 계산하는 기능
    - Handover
        - HO xApp이 RANSIM으로 HO request message를 보낸 경우 처리하기 위한 기능
    - E2node Coverage
        - MLB를 위한 E2node coverage 조정 기능
    - CLI endpoint, Telemetry
        - RIC에 telemetry information을 보고하는 기능

## RANSIM Peripherals
1. map view
    - gui
    - 시각화를 위해 RANSIM은 emulated된 E2Node와 UE의 정보를 RIC에 전달

2. change RAN scale
    - cli, gui : change number of UE
    - config, topo, cli : change number of E2 nodes

3. show E2E handover latency
    - prometheus, Grafana


### RANSIM demo

1. SD-RAN web GUI
    - map에서 자동차가 HO하는 demo

2. Grafana dashboard
    - 배치된 UE, cell 개수
    - HO information
        - e2e HO latency (평균, 90, 95, 99백분위수)
    - load balancing information
        - 각 cell이 서비스하는 UE 수의 표준편차 값 : 0에 가까울 수록 각 cell이 서비스하는 UE가 동일한 수
        - cli 임계값 10 = cell이 10개 이상의 UE를 가지면 과부하 셀로 간주됨을 의미
        - MLB 과정
            1. 3개의 과부화 cell
            2. MLB xApp은 과부화 cell의 coverage를 줄여 이웃 cell로 UE를 이동시킴
            3. 이웃 cell의 coverage를 늘려야 coverage hole이 생기지 않음
            4. UE 수에 따라 각 cell의 coverage가 확장, 축소됨
            5. 표준편차 값이 감소하면 성공임


## RANSIM external Architecture

<img  src="/assets/posts/sdran/3.png" alt=""/>

1. E2 interface
    - RANSIM과 RIC을 연결
    - 전달되는 정보 종류
        - E2 node ID
        - UE ID
        - E2 node information
        - neighbor cell
        - E2 node의 UE 지원
        - UE information
        - CQI
        - service cell and neighbor cell information
        - E2 node configuration
        - transmission power
        - HO message

2. gRPC protocol
    - RANSIM과 config, topo를 연결
    - config, topo : user또는 helm chart로부터 configuration 및 topology 정보를 전달
    

## RANSIM 관련 질문
1. UE의 mobility
    - UE가 무작위로 이동하는 모드
        - 지도에서 random 위치로 떨어트리고, random하게 직선으로 이동
    - UE가 도로 위를 이동하는 모드
        - 이는 계산 비용이 높아 보통 무작위 모드에서 테스트 진행

2. E2 node와 RIC의 통신
    - 원래는 gRPC protocol을 통해 이루어 졌지만 이 회의를 통해 E2 AP로 변경한 것 같습니다. 

3. E2 compliance에 의해 새로운 E2 sm model을 추가할 수 있음(무슨 말인지 잘 모르겠다)


## Revised RAN Simulator Architecture (for facilitate compliance with ORAN)
<img  src="/assets/posts/sdran/4.png" alt=""/>

0. interface
    - northbound inteface :  네트워크 제어 계층 - 어플리케이션 계층  ex.RIC과 xApp 간의 통신을 담당 
    - southbound inteface :  네트워크 제어 계층 - 데이터 계층 ex.RIC과 E2 node 간의 통신을 담당
    - xApp < north > RIC < south > Data


1. northbound
    - RANSIM에 elmulate 되어있는 E2 node의 E2 agent와 RIC 간의 통신을 담당
    - 과거 : gRPC
        - latency를 충족하기 위해
    - 현재 : ASN1을 통한 SCTP
        - compliance를 준수하기 위해 


## future work

1. 외부 simulator 사용
    - NS3, Quadriga와 같은 simulator 사용
    - 외부 simulator에 의해 생성된 dtat를 gRPC를 통해 RANSIM으로 전송하여 더 나은 모델을 제공함
    - 즉, 오프라인 데이터를 읽어 generator를 사용해 온라인처럼 보이도록하여 사용하여야함
    - xApp의 성능 확인 용도가 아닌 확장성 확인 용도로만 사용해야함

2. data plane emulation
    - 현재는 control plane만 emulation함
    - RIC의 확장성을 테스트할 때 필요할 수 있음

