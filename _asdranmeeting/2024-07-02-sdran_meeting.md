---
layout: single
title: "0702 SDRAN engineering team meeting"
categories: sdran
tag: [sdran]
---


## github action
### 그동안 작업했던 코드와 동일, 파일로 구분지어 놓은 것
1. release.yml
    - version-check, tag_version, push_images, bump-up-version

2. build-test.yml
    - job : build, test

3. code-scan.yml
    - job : version-check, link, license, fossa-check
    - version-check 새롭게 추가
        - Makefile에서 `./build/bin/version_check.sh all` 실행

### chart의 git action
- 크게 다른 점만 메모
1. release.yml
    - version-check : helm chart의 모든 directory보고, 변경된 directory의 version과 github에서 tag와 비교해서 같으면 error
    - tag_version : 각 chart 별로 version이 tag 되어있어야해서 조금 다름
2. build-test.yml
    - version-check : 각 chart 별로 version을 check 함
    - link : go link 말고 helm lint로 동작

### library의 git action
1. release.yml
    - image build가 없음
    - tag가 2번 : 전체 repository에 해당하는 tag, go가 포함된tag
        - `module github.com/onosproject/onos-a1-dm/go` : tag에 `/go`가 포함되어있어야 다른 go.mod에서 얘를 import할 수 있음


### 총 정리
- Makefile 등 정리 끝난 상태
- repository configuration 관련 변경사항은 setting 권한이 없어서 박사님이 진행
    - branch protection, pr 추가 설정 등
- OAI 관련 내용도 박사님이 완료한 상태 (github action 자체는 동일, Makefile build가  docker build해서 push하는 걸로 이루어져 있어서 엄청 오래걸린다고 하심)


### 추가 과제
- dependabot에 있는 pr을 정리하는 것
    1. 가장 하위 repo(libraray)의 `go.mod`에 가서 dependabot에 맞춰 version을 변경
    2. build 시행해보고 완벽하게 build 되도록 오류 수정
    2. 수정한 repo의 version 파일에 가서 version을 변경
    3. 상위 repo로 가면서 수정한 하위 repo version과 dependabot에 맞춰서 version을 변경
    4. 이 작업을 반복하여 상위 repo까지
    5. sdran 전체를 배포해서 실행되는 것 까지 확인하기

- 위의 과제 단순하게 맡겨주신 거
    1. ref의 go.mod의 version만 수정, 에러 상관없이 일단 pr 생성
    2. a1-dm, api, lib-go, exporter, sdk-go 등등 
    3. 해당 repo에서 branch 생성해서 작업 ㄱㄱ
    4. 박사님이 에러 수정하고 merge하면 다음 단계로 갈 예정

- gRPC관련 pr은 지금단계에서는 해결 불가하므로 다음과 같이 시행
    - 수정 전 : `conn, err := grpc.DialContext(ctx, "localhost:5151",`
    - 수정 후 : `//nolint conn, err := grpc.DialContext(ctx, "localhost:5151",`



## SIM writer (sdran 단말등록하기_외부유출x)
- https://drive.google.com/drive/folders/11oLGpobP9zWsqvM4BXRbiQaKKLjyamDT
    1. 파일 다 다운 받기
    2. GRSIMWirte.exe 실행파일 실행
    3. 카드리더기를 이용
    4. /template/onf_로시작하는 파일을 소프트웨어에서 load하면됨
    5. 이미 sample number가 들어가 있고, IMSI(임시번호)만 수정
    6. core와 sim 카드의 plmn이 동일하면 단말을 자동으로 인식



## RANSIMULTOR
### Scalable ransimulator

1. `pkg/model/load.go`
    - Unmarshal : yaml file을 통해 들어오는 값을 go에 있는 data structure(구조체, struct)로 바꿔주는 역할
```bash
indicationMessage := e2smkpmv2.E2SmKpmIndicationMessage{}   #빈 struct 선언
	s.NoError(proto.Unmarshal(indicationReport.Payload, &indicationMessage)) 
    #indicationReport.Payload(gRPC값)를 indicationMessage struct에 넣어라/근데 indicationReport.Payload는 struct 구조가 아니니까 Unmarshal을 이용해서 구조를 바꿔 넣어라!라는 의미
```

2. scalable의 의미
    - ransimulator : ransimulator는 1개의 pod로 띄워져 있고, 정의된 node, cell, ue가 1개의 pod 내에 모두 들어가 있는 상태
    - scalabe-ransim : 1개의 pod에 1cell+1node로 구성(node나 cell을 microservice 형태로 분리)
    - pod는 여러 server에 걸쳐 배포되지 못하고 1개의 server에서만 동작함
        - ex. cpu 10개인 server 5개
        - ransimulator : pod 1개로 구성되어 있기 때문에 서버 1개의 10개 cpu만 사용
        - scalable-ransim : pod 여러개로 구성되어 있기 때문에 서버 5개의 50개 cpu까지도 사용 가능
        - **scalable-ransim은 더 많은 computing resource를 사용할 수 있다**

3. model struct 안에 node, cell 등의 정보(global view)가 정리되어 있어야하고, global view의 내부 구성 요소 1개당 1개의 pod로 구성할 예정이다!

4. 동시성 문제 (/pkg/store/nodes/nodes.go)
    - store : map data struct를 각요소의 store로 packaging을 함
    - go data structure는 동시 접속 가능한 특성을 지님
        - 2개의 thread(goroutine)에서 1개의 data structure로 동시 접근 가능하고 이는 오류를 유발함
        - map data와 같은 것을 직접적으로 이용하는 상황이 발생
    - `mu sync.RWMutex` : 사용 중일 때 다른 사람이 사용하지 못하도록하는 장치를 이용해서 예방

5. agent vs agents.
    - agent
        - 1개의 element
        - connection과 같은 agent의 행동 정의
    - agents
        - 다양한 agent들을 생성




## 잡다한 정보
- unit_test는 있는 repo가 있고 없는 repo가 있음/ 없다고 이상한 repo가 아님 
- ransim의 yaml은 10진수로, cli에 표시되는 모든 수는 16진수
- stale.yaml 사용 x


## 다음 과제
- 지금까지 indication message(kpimon)를 파악했었음
- rc service model의 flow 파악하기
    - policy, insert message, control message를 분석 (mho, mlb use case관련)
