---
layout: single
title: "dashboard사용해보기"
categories: open source
tag: [gerrit]
---




            aiml-dashboard와 aiml-notebook을 사용하기 위해 UAV prediction usecase를 사용해보려고 했다.
            UAV Path Prediction guideline : https://lf-o-ran-sc.atlassian.net/wiki/spaces/AIMLFEW/pages/13697106/New+usecase+-+UAV+Path+Prediction

## container 내부와 외부는 token 이름만 다른 점을 기억하면 편함
1. 내부 : `-t $INFLUXDB_ADMIN_USER_TOKEN`
2. 외부 : `-t $INFLUXDB_TOKEN`
    - ${INFLUXDB_TOKEN}
        - influxdb 설치시 미리 설정해두었던 변수
        - 재설정 방법
            1. token 추출
                - `kubectl exec -it my-release-influxdb-85888dfd97-bjr5g -- cat bitnami/influxdb/influxd.bolt | tr -cd "[:print:]" | grep -o '"token":"[^"]*"' | awk -F\" '{print $4}'`
                <!--   hMJrHRCFdQHXohaE6NwL   -->
            2. token 설정
                - `export INFLUXDB_TOKEN=<token>`
            3. 확인 방법 : `echo $INFLUXDB_TOKEN`


## influxdb에 training에 사용할 data 등록
1. tocken echo
2. bucket 생성
    - `kubectl exec -it my-release-influxdb-85888dfd97-bjr5g -- influx bucket create -n DLData -o primary -t $INFLUXDB_TOKEN`
3. data 등록
    1. UAV_insert.py config 변경
            - DATASET_PATH = 'UAV_Dataset.csv'
            - INFLUX_IP = 'localhost'
            - INFLUX_TOKEN = '<Tocken>' 
    2. influxdb에 data 입력
        1. influxdb port-forward
            - `kubectl port-forward svc/my-release-influxdb 8086:8086`
        2. 가상환경 설정
            - `apt install python3.10-venv`
            - `sudo -i` 후에 원하는 directory로 이동
            - `python3 -m venv .venv`
            - `source .venv/bin/activate`
            - `pip3 install pandas`                
            - `pip3 install influxdb_client`
         3. data 등록
            -  `python3 UAV_insert.py`


## jupyter notebook을 이용해 pipline.yaml 파일 생성
- UAV_pipeline.ipynb를 실행시키면 pipeline이 yaml 형식으로 생성된다.
- UAV_pipeline 일부를 수정해 주어야하는 해당 내용은 추후에 작성하도록하겠다.


## 이제 dashboard를 이용할 수 있음
1. Create Feature Group
    - installation guide에 해당 내용이 table로 정리되어있으니 참고하자
    - <img  src="/assets/posts/ossca/8.png" alt=""/>
    - DME를 사용하지 않는 방법으로 하면된다.

2. Training Job 생성
    - 이 부분도 installation guide에 table로 정리되어있다.
    - <img  src="/assets/posts/ossca/9.png" alt=""/>
    - model management service는 사용하지 않는 방법으로 한다.
    - training job 이름
        - 대문자와 '-'를 사용하면 data extraction에 무조건 실패한다.

3. training 완료
    - 아직 완성 못함


## 완성된 model을 kserve를 이용해 upload하기





## model 사용해보기