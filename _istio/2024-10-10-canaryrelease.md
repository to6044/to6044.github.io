---
layout: single
title: "3.Canary Release"
categories: k8s
tag: [istio]
---



Canary Release는 새로운 소프트웨어 프로젝트에서 새로운 버전을 배포할 때 사용하는 전략으로, 모든 사용자에게 새 버전을 한 번에 배포하지 않고, 일부 사용자에게만 새로운 버전을 제공한 후에 문제가 없는지 확인하는 방식이다.   
   
microservice를 구축할 때 새로운 버전을 배포하는 일은 매우 빈번하게 일어나며, 새롭게 업데이트하는 파일이 제대로 작동하는 지에 대한 의문이 있을 때 이 Canary Release를 사용함녀 좋을 것 같다.    
   
특히 K8S에서 이를 구현하는 방법은 약간 야매? 방법인데 설명하자면 A라는 pod의 버전 1을 사용하다가 버전 2로 업데이트하고 싶을 때 버전 1과 2 pod를 모두 동시에 띄우는 것이다. 이렇게 하면 사용자가 사용할 때 50% 정도의 응답은 버전 2를 거치게 되어 사용자의 일부는 새로운 버전의 pod를 사용하게 되고 문제를 확인할 수 있다.   

하지만 보통 새로운 버전이 전체의 1%~10%가 되도록 하기 때문에 이러한 방법으로 구현하면, 띄워야하는 pod의 수가 매우 많아 진다. 하지만, istio를 사용하면 좀 더 안정적으로 canary release를 사용할 수 있다. 
   

먼저 kiali에서는 app이 가장 큰 인식 단위인데, yaml 파일을 작성할 때 spec의 metadata의 하위 라벨인 label에 `app: `을 사용해야 kiali에서 이를 인식할 수 있다. 그리고 label에 `version: <별명>`을 사용하면 한 서비스 내에서 생성한 서로 다른 pod를 kiali에서 잘 관리할 수 있다.    
<img  src="/assets/posts/istio/12.png" alt=""/>      
   
version을 설정하면 다음과 같이 보이게 된다. (Versioned app graph에서)   

<img  src="/assets/posts/istio/11.png" alt=""/>      
   
이제 pod 갯수를 늘리지 않고 신 버전과 구 버전의 비율을 조절할 건데, kiali graph의 해당 service의 detail 상단에 있는 Action에 들어가면 `Create Weighted Routing`이 있다. 이를 이용하여 다음과 같이 사용 비율을 조절할 수 있다. 이 때 비율은 확률로 구현되는 것이기 때문에 적은 시행 횟수에서는 정확한 비율을 구현할 수 없을 수 있으니 감안해야한다.

<img  src="/assets/posts/istio/13.png" alt=""/>      

이렇게 kiali에서 routing을 설정해주면 istio config에 Virtual Service와 Destination Rule이 생성되는 것을 확인할 수 있다.   

<img  src="/assets/posts/istio/14.png" alt=""/>      
