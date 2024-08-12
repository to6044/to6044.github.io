---
layout: single
title: "Non-RT RIC RAN PM Measurement 정리"
categories: open source
tag: [gerrit]
---

## Non-RT RIC RAN PM Measurement
1. 개요
    - Non-RT RIC은 PM 측정을 처리하기 위한 solution을 제공함.
    - PM 보고서는 XML 파일로, RAN에서 수집되어 저장되며 rApp은 원하는 resource를 구독하여 data를 저장된 파일을 통해 정보를 얻을 수 있음

2. 구성요소

    <img  src="/assets/posts/ossca/2.png" alt=""/>
    - PM Data File Collector : RAN에서 data file(XML) 가져옴
    - PM File Converter : data형식 변경(XML -> JSON), 구조 및 내용 변경 x
    - PM Producer : data 중 subscriber(rApp)가 원하는 data만 filtering 후 배포
    - Influx Logger : 선택된 PM measurement를 시간 순으로 저장함 (시계열 database)
    - HTTPS-SERVER : test용, RAN node에서 data 파일 전송하는 동작을 simulation
    - third party products
        - Minio : object 저장소로 file을 저장
        - Kafka  : data, message를 전송
        - Influx : 시계열 database로, PM measurement를 시간에 따라 저장

3. Data flow

    <img  src="/assets/posts/ossca/3.png" alt=""/>

    1. VES event 송신
        - PM report는 VES event에 포함
        - data 이동경로 : RAN node -> VES collector

    2. VES event 수신
        - kafka의 특정 topic에 VES event가 포함
        - Data File Collector는 topic을 모니터링하여 event를 수신
        - message 이동경로 : VES collector -> Data File Collector

    3. PM report 다운로드 받기
        - Data File Collector는 RAN node로부터 RM report file(XML)을 다운로드
        - VES event에서 지정된 전송protocol을 사용
        - data 이동경로 : RAN node -> Data File Collector
    
    4. PM report 저장
        - Data File Collector가 다운로드한 PM report를 Minio에 저장
        - data 이동경로 : Data File Collector -> Minio

    5. 저장된 파일(PM report)관련 정보전달
        - Kafka topic을 통해 저장된 파일 object에 대한 정보를 받음
        - message 이동경로 : Data File Collector -> Data Converter

    6. 저장된 파일 가져오기
        - Data Converter가 파일 저장소에서 XML 파일을 읽어옴
        - data 이동경로 : Minio -> Data Converter

    7. 파일 변환
        - Data 형식 변환 (XML -> JSON)하여 gzip으로 압축
        - 파일 형식만 변환 o, 구조 및 내용 변환 x

    8. 새로운 report 사용가능 알림
        - 완성된 JSON 형식의 PM report가 7번에서 완성
        - 이를 Kafka topic을 통해 Data Producer에게 사용가능한 PM report가 생성되었다는 것을 알림
        - message 이동경로 : Data Converter -> Data Producer

    9. 저장된 파일 가져오기 
        - Data Producer가 파일 저장소에서 JSON 파일을 읽어옴
        - data 이동경로 : Minio -> Data Producer

    10. 구독자에게 데이터 전달
        - Data Producer가 구독자(rApp)가 구독한 데이터만 필터링하여 rApp에 전달
        - Kafka를 통해 전달
        - data 이동경로 : Data Producer -> rApp
    
    11. PM Data를 시계열 DB에 저장
        - PM data를 influxDB에 저장함
        - rApp은 InfluxDB에 언제든지 접근 가능
        - data 이동경로 : Data Influx Logger -> InfluxDB


            cf. 각각의 component들은 kafka를 통해 각 요소가 데이터를 이용할 수 있다는 메세지를 받은 후에, Minio에서 data를 읽어와 각자의 task를 수행하는 메커니즘인 것 같다.


## PM Data Subscription (control flow)

<img  src="/assets/posts/ossca/4.png" alt=""/>

    - 구독은 PM measurement 데이터가 ICS를 사용하여 Information Job을 생성함으로써 설정됨.
        1. Information Coordination Service (ICS)
            - data subscription을 관리하는 중재자 역할
            - DME(Data Management Exposure) in O-RAN
        
        2. Information Job
            - 어떤 data가 필요한지, 어디서 수신할지 정의

    - 먼저, rApp과 Data Influx Logger는 원하는 data를 ICS를 통해 구독함
    - 그 후, PM Data Producer는 구독을 확인하고 맞는 data를 구독자에 전달함
    - BUT, Data Collector는 모든 PM data를 가져오기 때문에 구독을 사용하지 않음


## PM Subscriber design time dependencies
<img  src="/assets/posts/ossca/5.png" alt=""/>







## Kafka
- 대규모 실시간 data 스트리밍을 위한 분산 이벤트 스트리밍 플랫폼
- opensource로 2011년에 공개
- Apache Software Foundation의 프로젝트
- 주요 기능
    - 실시간 데이터 처리 : 실시간으로 data를 수집하고 처리하는데 사용
    - 데이터 파이프라인 구축 : 서로 다른 시스템 간의 데이터 이동을 쉽게할 수 있도록 도와줌
    - 이벤트 소싱, CQRS 패턴 : 시스템 상태 변화를 기록하고 필요할 때마다 재구성
    - 분산 log 저장소 : data가 여러 broker에 걸쳐 분산 저장되어 장애에도 안전

- 구성 요소
    - Producer
        - 데이터를 Kafka 토픽에 게시하는 클라이언트
        - 데이터 메시지를 생성하여 Kafka broker에 전송

    - Broker
        - Kafka 클러스터를 구성하는 서버
        - 데이터를 저장하고 소비자에게 데이터를 제공
        - 여러 브로커가 모여 클러스터를 형성하며, 데이터의 복제와 분산 저장을 담당

    - Consumer
        - Kafka에서 데이터를 읽어들이는 클라이언트
        - 특정 토픽으로부터 메시지를 수신하여 처리

    - Topic
        - 메시지가 게시되는 카테고리나 피드
        - 각각의 토픽은 파티션으로 나뉘어 데이터가 분산 저장

    - Partition
        - 토픽 내에서 메시지를 나누어 저장하는 단위
        - 파티션 덕분에 Kafka는 확장성과 높은 처리량을 제공

    - ZooKeeperKafka 
        - 클러스터의 메타데이터를 관리하고, 브로커 간의 협력을 지원하는 서비스
        - 그러나, 최근 Kafka가 자체적인 메타데이터 관리 시스템을 도입하여 Zookeeper 의존성을 줄여가고 있음

