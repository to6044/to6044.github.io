---
layout: single
title: "2.gitaction"
categories: github
tag: [github]
---


## CI/CD

<img  src="/assets/posts/github/1.png" alt=""/>

1. Continuous integration 지속적인 통합 
    - 코드 작성 -> 코드 테스트 -> 코드 빌드 -> 배포 -> 버그 -> 
    - 위의 cycle을 짧은 주기로 자동화해서 code를 빠르게 개선시키기 위함. (작성, 테스트, 빌드)

2. Continuous delivery / deployment 지속적인 제공 / 배포
    - Delivery : 개발환경에 배포되는 경우 (가장 마지막 단계인 운영환경 배포는 수동으로)
    - Deployment : 운영환경에 배포되는 경우 (가장 마지막 단계인 운영환경 배포까지 자동)
            **결국 짧은 주기로 자동화하는 것이 목표!**


## Github action에 대해서

<img  src="/assets/posts/github/2.png" alt=""/>

- 자동화된 script를 작성하는 것
- 위에서 가장 까다로운 과정인 버그를 빠르게 해결하기 위한 방법의 일환으로 github action을 사용
- 코드를 가상 머신에서 돌려서 결과를 확인. 
- Workflow는 actions보다 더 큰 개념, actions가 workflow에 속하는 개념(workflow > actions)


<img  src="/assets/posts/github/3.png" alt=""/>
