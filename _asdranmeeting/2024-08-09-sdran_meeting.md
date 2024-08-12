---
layout: single
title: "0809 SDRAN engineering team meeting"
categories: sdran
tag: [sdran]
---

현재 version 그대로 1.5 release 예정
(go version을 올리면 build kit 사용이 어려워지기 때문, 너무 시간 많이 들 것 같음)

onos project를 빼고 sdran만 따로 빠져서 진행

=> onos 모든걸 옮겨야함


- E2SM 



## 이번 주 할일

1. sdran helm chart -> 최신 버전으로 업데이트해야함
    - onos의 각 repository에 가서 가장 최신 version(tag)를 확인하여 sdran helm chart의 yaml 파일에서 각 repository의 version 수정해서 PR 생성


2. aether-onramp를 이용해서 sdran 구성하는 과정 문서화 (command, 결과, 주의사항)