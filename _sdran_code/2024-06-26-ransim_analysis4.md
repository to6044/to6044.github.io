---
layout: single
title: "9.manager.go"
categories: RANSIM
tag: [SDRAN PROJECT]
---


## manger.go

1. manger의 설정 변수인 구조체(struct) type의 Config 변수 선언
    - `type Config struct`



2. Manager 구조체 정의
    - ```bash
    type Manager struct {
        modelapi.ManagementDelegate
        config         Config
        agents         *agents.E2Agents
        model          *model.Model
        server         *northbound.Server
        nodeStore      nodes.Store
        cellStore      cells.Store
        ueStore        ues.Store
        routeStore     routes.Store
        metricsStore   metrics.Store
        mobilityDriver mobility.Driver}
        ```
    - server : gRPC server를 의미
    - 각 line은 Manger 구조체의 field를 의미하며 앞에 정의된 변수를 이용해 호출할 수 있음. (`m.mobilityDriver`)



3. "Creating Manager"
    - `func NewManager(config *Config) (*Manager, error)`
    - Config 값을 받아 Manager 구조체를 생성
    - Manager로 mgr, error로 nil이 출력
	- 값 설정
        - `config: *config`
		- `agents: nil`
		- `model:  &model.Model{}`



4. "Running Manager"(m.Run method)
    - [Start method]를 호출해 manager 시작
    - Start가 error 반환시 로그에 기록



5. "Start Manger"(m.Start method)
    - [model의 load method]로 model data를 load
    - [m의 initModelStores]로 model 저장소 초기화
    - [m의 initMetricStore]로 metric 저장소 초기화
    - [m의 startNorthboundServer]로 gRPC server 시작
    - [m의 startE2Agents]로 E2 agents 시작
    - mobility 관련 (pakage 이름은 mobility인데 이들이 정의된 파일 이름은 driver.go)
        - `mobilityDriver = mobility.Driver` (mobility paakage 안의 Driver interface)
        - `m.mobilityDriver = mobility.NewMobilityDriver(...)`
            - 좌 : mobilitydriver는 Manger인 m의 field
            - 우 : mobility package에서 정의한 NewMobilityDriver 함수를 사용 (/pkg/mobility/driver.go)
            - manager m의 field인 mobility driver의 NewMobilityDriver 함수를 이용해 다양한 field를 채움
            - manager의 각 field들은 다른 pakage에 go file로 정의되어 있음
        - `m.mobilityDriver.GenerateRoutes(...)`
            - `m.mobility.Driver.GenerateRoutes와 같음
            - /pkg/mobility/driver.go에 Driver interface 안에 있는 [GenetrateRoutes method]
            - mobility 관련된 route를 생성하는 것 같음
            - context 라이브러리를 이용
            - 속도 관련 값은 상수로, 경로관련 변수들은 m.model에서 받아옴
        - [Driver의 Start method]로 mobilitydriver를 시작
    
    

6. "Closing Manager"(m.Close)
    - [m의 stopE2Agents]로 E2 agent 정지
    - [m의 stopNorthboundServer]로 gRPC server 정지
    - [Driver interface 안의 mobilityDriver method]로 mobility driver 정지



7. model 관련 저장소 초기화 (m.initModelStores method) - 5번에 실행됨
    - node, cell, ue, route를 초기화
    - 각 함수들은 각 pkg에 있고 나중에 자세히 들여다 보자.
    - manager의 field `m.nodeStore`에 node pkg의 NewNodeRegistry함수를 이용해 초기화
        - 입력 : m의 field인 model의 field인 Nodes `m.model.field`
    - `m.cellStore`에 NewCellRegistry함수를 이용해 초기화
        - 입력 : `m.model.Cells`, `m.nodeStore`
    - `m.ueStore`에 NewUERegistry함수를 이용해 초기화
        - 입력 : `m.model.UECount`, `m.cellStore`, `m.model.InitialRrcState`
    - `m.routeStore`에 NewRouteRegistry를 이용해 초기화

8. metric 저장장소 초기화(m.initMetricStore method)


9. gRPC server 시작, service 등록 (m.startNorthboundServer method, type error)
    - pakage 이름은 northbound인데 이들이 정의된 파일 이름은 server.go
    - northbound pkg의 NewServer 함수를 이용해 m.server를 생성.
        - 입력은 m의 config field에 있는 값들을 이용 (CAPATH, keyPath ...)
    - [server의 AddsService method]를 이용해 서비스를 등록
        - nodeapi는 nodes pkg에 정의된 NewService 함수를 이용
            - `nodeapi.NewService(m.nodeStore, m.model.PlmnID)` 요런식으로
    - server 시작
        1) `doneCh := make(chan error)` : error type의 doneCh 채널 생성
        2) 이름이 없는 goroutine, 이름이 없는 func
            - [Server의 Serve method]를 이용해 서버를 시작
                - NB gNMI 서버 시작 (Northbound gRPC Network Management Interface)
                - 네트워크 장비에서 데이터를 수집하고 관리 명령을 전송
                - 입력 : func(started string) - 콜백함수로 이벤트가 완료되면 호출됨(여기서는 로그 출력함)
            - doneCh를 통해 서버 시작, error를 전달
        3) server가 시작되면 doneCh를 닫아 알림




10. E2 agent 생성 + 실행 (m.startE2Agents method, type error)
    - agent pkg의 NewE2Agents 함수로 m.agent를 생성
        - 입력 : m의 field들
        - 출력 : m.agent, err
    - 생성된 E2Agents인 m.agent를 start method로 실행시킴


11. E2 agent 정지 (m.stopE2Agents method, x)
    - E2Agents의 method인 Stop은 error type을 반환할 수 있음
    - 그러나 `_ = m.agents.Stop()`에서 `_`는 Stop이 반환한 error를 저장하거나 처리하지 않고 무시한다는 것을 의미


12. gRPC server 정지 (m.stopNorthboundServer method, x)
    - ```bash
    func (s *Server) Stop() {
        s.server.Stop()}
        ```
    - 윗부분 이해가 안됨


13. ransim 정지 후 관련 데이터 지움 (m.PauseAndClear method, x)
    - ransim 정지 = E2Agents 중지
    - node, cell, metrics Store method로 비움


14. (m.LoadModel method, type error)
    - manager의 field인 model을 업데이트


15. (m.LoadMetrics method, type error)
    - manager의 field인 metric을 업데이트


16. (m.Resume method, x)
    - 1초 sleep 후에 northboundServer를 이용해 gRPC server를 멈췄다가 재시작
    - 후에 E2Agent를 다시 시작함

