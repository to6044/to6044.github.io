---
layout: single
title: "5.Destination Rule"
categories: k8s
tag: [istio]
---



Destination Rule도 virtual service와 같이 istio에서 제공하는 개념으로 모두 traffic 관리에 사용된다.
virtual service를 통해 traffic이 이동할 service를 지정해 주었다면, 그 다음에 작동하는 것이 DR이다.   

vs의 yaml file을 보면, traffic이 향할 특정 service의 dns를 명시해놨으며, service 내부에서 각 subset에 weight를 지정해 놓았다. 여기서 subset이 어떤 것을 의미하는지를 dr에서 작성해 주는 것이다. 즉, subset이라고 지정해 놓은 곳에 해당하는 pod를 label을 통해 연결지어 줌으로써 load balancing 역할을 완성시킬 수 있다.    
 
여기서 주의할 점의 dr에 설정된 subset의 name과 vs에 설정된 subset : <name>이 동일해야며, 이는 어떤 서비스나 pod를 가리키는 것이 아닌 사용자 설정값이기 때문에 자유롭게 작성해줘도 되는 부분이다.


```yaml
kind: DestinationRule
apiVersion: networking.istio.io/v1alpha3
metadata:
  name: fleetman-staff-service
  namespace: default
#  uid: 3058203e-086c-4b53-97f5-5c5af2f80a79
#  resourceVersion: '233430'
#  generation: 1
#  creationTimestamp: '2024-10-11T06:40:10Z'
#  labels:
#    kiali_wizard: weighted_routing
#  managedFields:
#    - manager: Go-http-client
#      operation: Update
#      apiVersion: networking.istio.io/v1alpha3
#      time: '2024-10-11T06:40:10Z'
spec:
  host: fleetman-staff-service.default.svc.cluster.local   # Service
  subsets:
    - labels:           # SELECTOR
        version: safe   # find pods with label "safe"
      name: safe
    - labels:
        version: risky
      name: risky
```