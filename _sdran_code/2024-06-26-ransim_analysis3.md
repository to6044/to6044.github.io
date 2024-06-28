---
layout: single
title: "9.ransim.go"
categories: RANSIM
tag: [SDRAN PROJECT]
---

## ransim.go

1. `package main`
    - 프로그램의 진입점
    - 독립 실행형 application을 생성(library, package 파일이 아닌!)
    - 해당 파일에 main 함수가 같이 존재함
    - 해당 파일의 directory에서 명령어(`go build`, `go run`)을 실행하면 컴파일 할 수 있음


2. `import`
    - 사용할 package (내장 or 외부)
    - 내장 : `flag`, `math/rand`, `time`
    - 외장 : `logging`, `manager`
    - 해당 package에서 사용가능한 명령어를 이용함. 라이브러리 같은 개념으로 생각 가능


3. 사용자 정의 type + method 정의
    - 사용자 정의 type
        - `type <사용할 이름> <원하는 유형의 type>`
        - `type arrayFlags []string`
    - method 정의
        - arrayFlags의 pointer를 받아 string type인 'my string representation'을 반환하는 String이름의 method
        - ```bash
        func (i *arrayFlags) String() string {
            return "my string representation"}
            ```
        - arrayFlags의 pointer를 받아 string type의 value 값을 arrayFlag에 값을 입력하는 Set이름의 method
        - ```bash
        func (i *arrayFlags) Set(value string) error {
            	*i = append(*i, value)	return nil}
            ```

            
4. main 함수
    - `var serviceModelPlugins arrayFlags` : arrayFlags type의 serviceModelPlugins 이름의 변수 선언
    - `flag.Var(&serviceModelPlugins, "serviceModel", "(repeated)")` : 사용자 정의 type을 flag에 등록
        - `&serviceModelPlugins` : flag 값 저장 장소의 포인터
        - `serviceModel` : command line에서 사용되는 flag 이름 (-serviceModel 이렇게 사용되겠지)
        - `names of service model plugins to load (repeated)` : 도움말 메세지에 표현되는 내용

    - 기본 flag 정의
        - `할당 변수 := flag.<변수 type>(flag이름, defalut값, 설명문)`
        - ```bash
        caPath := flag.String("caPath", "", "path to CA certificate")
        keyPath := flag.String("keyPath", "", "path to client private key")
        certPath := flag.String("certPath", "", "path to client certificate")
        grpcPort := flag.Int("grpcPort", 5150, "GRPC port for e2T server")
        modelName := flag.String("modelName", "model", "RANSim model file/resource name")
        metricName := flag.String("metricName", "", "RANSim metric file/resource name")
        hoLogic := flag.String("hoLogic", "local", "the location of handover logic {local, mho}")
        ```

    - `flag.Parse()` : 정의된 flag를 파싱함 = commandline에 입력된 값을 읽고 각 flag에 해당하는 변수에 값을 설정.


5. flag 값 중 hoLogic 값 검증
    - hoLogic이 local이나 mho이 아닌 경우를 오류로하고 프로그램을 종료


6. manager 구성 및 실행
    - `cfg := &manager.Config` : manager의 pointer인 cfg에 앞서 파싱해서 가져온 flag 값들을 입력
    - `	mgr, err := manager.NewManager(cfg)` : cfg 값을 새로운 manager 객체를 생성.
    - 객체는 mgr, 오류는 err에 저장


7. 매니저 실행을 위한 goroutine 간의 channel 
    - ```bash
    if err == nil {
        mgr.Run()
        <-ready
        mgr.Close()}
        ```
    - err가 비어있으면 manager 생성이 잘 됬다는 의미
    - mgr.Run()으로 manager를 실행함
    - ready channel을 통해 다른 goroutine에서 값을 받기 전까지 main함수의 실행이 멈춤
    - 아직 뒤를 다 안봐서 모르겠는데 다른 goroutine에서 어떤 값을 ready에 보내어 close가 실행되면 manager가 멈추는 것으로 예상