---
layout: single
title: "12.EC2_autoscaling"
categories: AWS
tag: [cloud]
---


## EC2를 잘 활용하기 위한 service들을 공부할 예정


## Scaling
- scaling : instance (computing power)를 늘리는 것

1. Vertical Scale (Scale up)
    - instance의 성능을 향상시킴
    - 성능에 비해 비용이 기하급수적으로 증가

2. Horizontal Scale (Scale Out)
    - 성능향상이 아닌 규모를 늘림
    - 성능과 비용이 정비례

3. 즉, cloud 환경에서는 Scale up이 아닌 Scale Out을 고려해야함
    - 비싼 걸 사용 하는 것이 아닌 저렴한 instance의 규모를 줄이거나 늘려서 유연하게 대처



## AWS Auto Scaling
- cloud의 Horizontal Scale을 지원하기 위한 service
- 자동으로 용량을 조정하여 최대한 저렴한 비용으로 안정적이고 예측 가능한 성능을 유지.
- EC2, DDB, Spot Fleet, Aurora, ECS Auto Scaling