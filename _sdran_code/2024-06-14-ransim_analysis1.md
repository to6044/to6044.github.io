---
layout: single
title: "9.RANSIM 분석1"
categories: RANSIM
tag: [SDRAN PROJECT]
---

## helm chart가 어떤 경로로 작동되는가

1. `aether-onramp/var/chart_ref`
    - 원래 sdran에서는 helm public chart에 있는 helm chart를 참조했음
    - 지금 mcl 서버에는 `sdran-helm-chart`에 있는 helm chart를 참조함

2. `sdran-helm-chart`의 정보 입력 순서
    - `sdran의 value.yaml` <- `Chart.yaml` <- `해당 directory의 value.yaml`


## RANSIMULATOR는 어떤 경로로 작동되는가

0. goflag?
    - cli에서 명령으로 외부에서 입력하는 값을 선언할 때 사용
    - 해당 project에 외부 입력 값들이 yaml 파일이 정리되어 있음


1. `cmd/ransim/ransim.go`
    - 가장 상위 code
    - 선언된 manager에 이미 설정되어있는 **config(cfg)**을 입력 -> goflag를 이용해서 
        - **config(cfg)**이 입력된 것을 확인할 수 있는 위치 : `sdran-helm-chart/ranasimulator/template/deployment.yaml`의 arg값


2. `pkg/manager.go`
    - ransimulator의 manager
    - manager에 필요한 값들을 집어 넣기 위한 깡통
        - manager config 깡통
        - Manger 깡통 
        - manger에 필요한 다양한 요소 받아옴 : `initModelStoresmodel`, `NewCellRegistry`, ue, rout, metrics
        - manager에 필요한 다양한 요소 실행 : `startNorthboundServer`, e2agent
    - **아직 정확한 순서는 좀 더 자세히 봐야할듯**



    ## cf.
    - C++의 call by reference가 무엇인가
    - golang의 viper가 무슨 역할을 하는가(goflag와 관련)
        - `manager.go` 안에 있는 빈 깡통들에 이미 선언되어있는 값을 받아서 넣어주는 library 역할인거 같다
    - container 안에 있는 yaml파일은 무슨 역항르 하는가? (1번과 관련)


