---
layout: single
title: "4.Virtual Service"
categories: k8s
tag: [istio]
---



## Virtual Service
service mesh에 사용자 지정 routing rule을 구성할 수 있게 된다.   

Virtual Service가 cluster 내부에서 어떻게 작동하는지를 먼저 파악하기 위해 service는 어떻게 작동하는지 알아보자.   
특정 client(다른 pod or frontend와 같은)가 특정 pod를 호출하려고 한다면, 먼저 service를 검색하고, 해당 service에 해당하는 다수의 pod 중에서 특정 pod와 label과 selector가 일치하는 pod를 선택하는 과정이 일어난다.   

Virtual Service를 cluster에 적용하면 해당 정보는 istio의 control plane에 전달되며, 이는 각 pod에 배포되어 있는 proxy container에 전달되어 해당 virtual service가 즉시 적용된다. proxy를 주입하면, 모든 traffic은 envoy proxy를 거쳐 가기때문에 이러한 서비스가 가능하다.   

즉, istio의 virtual service는 k8s의 service와는 완전히 다른 것이다.     

virtual service는 pod의 갯수 등 환경을 변화시키지 않고도 각 pod를 사용하는 비중을 yaml 파일 만으로 변경할 수 있다는 장점이 있다.    

kiali에 있는 virtual service의 yaml file을 가져오면 다음과 같다.   


```yaml
kind: VirtualService
apiVersion: networking.istio.io/v1alpha3
metadata:
  name: fleetman-staff-service
  namespace: default
  uid: 2455278e-eebf-469c-9587-57d0301f07e4
  resourceVersion: '68084'
  generation: 1
  creationTimestamp: '2024-10-10T07:03:57Z'
  labels:
    kiali_wizard: weighted_routing
  managedFields:
    - manager: Go-http-client
      operation: Update
      apiVersion: networking.istio.io/v1alpha3
      time: '2024-10-10T07:03:57Z'
spec:
  hosts:
    - fleetman-staff-service.default.svc.cluster.local
  http:
    - route:
        - destination:
            host: fleetman-staff-service.default.svc.cluster.local
            subset: safe
          weight: 90
        - destination:
            host: fleetman-staff-service.default.svc.cluster.local
            subset: risky
          weight: 10
```   

해당 yaml file에서 istio 관련된 내용을 삭제하여 새로운 yaml로 만들어 로컬에서 배포하면, 해당 vs 기능을 kiali를 사용하지 않고도 가능한데, 아래는 수정한 yaml 파일이고, weight를 변경하여 apply해주면 canary release의 형태로 사용할 수 있다.


```yaml
kind: VirtualService
apiVersion: networking.istio.io/v1alpha3
metadata:
  name: fleetman-staff-service         #just a name for this virtualservice
  namespace: default
spec:
  hosts:
    - fleetman-staff-service.default.svc.cluster.local       #service DNS name
  http:
    - route:
        - destination:
            host: fleetman-staff-service.default.svc.cluster.local
            subset: safe
          weight: 90
        - destination:
            host: fleetman-staff-service.default.svc.cluster.local
            subset: risky
          weight: 10
```