---
layout: single
title: "standalone 사용해보기"
categories: open source
tag: [gerrit]
---





1. pipline yaml 파일 생성안될 때

```bash
E0826 14:30:16.325165       7 pipeline_upload_server.go:299] Failed to upload pipelines. Error: Failed to create a pipeline and a pipeline version. The pipeline already exists.: Failed to create a pipeline and a pipeline version: Already exist error: Failed to create a new pipeline. The name UAV_pipeline_test0 already exists. Please specify a new name
```

- 이미 파이프라인이 존재한다고한다.
- 그래서 다른이름으로 바꿔봐도 계속 이런일이 일어난다.... 일단 mount가 어디로 연결되어있는지 확인해보자
- kubflow pipeline 목록을 확인할 수 있는 UI가 포트 80번에 존재해서 확인가능하다!
    - `k -n kubeflow port-forward svc/ml-pipeline-ui 8080:80`
    - <img  src="/assets/posts/ossca/17.png" alt=""/>

- 그럼 여기서 삭제하고 다시 파이프라인을 생성해보면 오류가 생기는지 확인해보자
    - <img  src="/assets/posts/ossca/18.png" alt=""/>
    - 삭제가 안된다....
- 근데 허무하게 해결됬다. 
    - pipeline_name ="KT_pipeline0"
    - 이 부분에서 이름만 바꾸니까 되네... 왜이런지 모르겠다.....
    - 그래도 ml-pipeline-cc79859f4-rgv5g pod에 조금의 문제가 존재하는 건 인지해야겠다.
    - <img  src="/assets/posts/ossca/19.png" alt=""/>
    - 엥 근데 dashboard 가니까 다 떠있다... 진짜 뭔지 모르겠다. 일단 다음 진행 해봐야겠다.


2. data extraction에서 training으로 안 넘어 갈때
    - error log
        ```bash
        2024-08-27 04:20:04,462 | trainingmgr_main.py 1686 async_feature_engineering_status() |  ERROR | Failure during procesing of DATAEXTRACTION_JOBS_CACHE,HTTPConnectionPool(host='%3cfill%20ip%20of%20host%3e', port=32002): Max retries exceeded with url: /trainingjob/dataExtractionNotification (Caused by NameResolutionError("<urllib3.connection.HTTPConnection object at 0x7ff1f4461ed0>: Failed to resolve '%3cfill%20ip%20of%20host%3e' ([Errno -2] Name or service not known)"))
        2024-08-27 04:20:04,534 | trainingmgr_util.py 43 response_for_training() |  DEBUG | Training job result: 500 HTTPConnectionPool(host='%3cfill%20ip%20of%20host%3e', port=32002): Max retries exceeded with url: /trainingjob/dataExtractionNotification (Caused by NameResolutionError("<urllib3.connection.HTTPConnection object at 0x7ff1f4461ed0>: Failed to resolve '%3cfill%20ip%20of%20host%3e' ([Errno -2] Name or service not known)"))(trainingjob name is kt1) False
        ```
    - 이건 training host의 localhost가 설정이 안되있어서 그렇다.
    - installation을 확인하면 example_recipe_latest_stable.yaml 파일에서 ipadress를 변경해 줘야하는데 내가 깜빡하고 변경을 안해줬다... 이런;;;;
        ```bash traininghost:
                ip_address: localhost
        ```


3. 이젠 training이 안된다. 뭐가 한 번에 되는게 없다....
    - training이 실패하면 다음과 같이 error pod가 생성된다.
    - <img  src="/assets/posts/ossca/20.png" alt=""/>
    - ```bash
        2024-08-27 05:56:19,073 | model_metrics_sdk.py 154 upload_metrics() |  DEBUG | kt3 bucket is creating
        2024-08-27 05:56:19,081 | model_metrics_sdk.py 156 upload_metrics() |  DEBUG | kt3 bucket is created
        2024-08-27 05:56:19,081 | model_metrics_sdk.py 162 upload_metrics() |  DEBUG | putting object inside bucket
        2024-08-27 05:56:19,091 | model_metrics_sdk.py 168 upload_metrics() |  DEBUG | object is put inside bucket
        2024-08-27 05:56:22,592 | model_metrics_sdk.py 175 upload_metrics() |  ERROR | Traceback (most recent call last):
        File "/usr/local/lib/python3.8/site-packages/modelmetricsdk/model_metrics_sdk.py", line 170, in upload_metrics
            response = self.client.list_objects(Bucket=export_bucket, Prefix="")
        File "/usr/local/lib/python3.8/site-packages/botocore/client.py", line 569, in _api_call
            return self._make_api_call(operation_name, kwargs)
        File "/usr/local/lib/python3.8/site-packages/botocore/client.py", line 1023, in _make_api_call
            raise error_class(parsed_response, operation_name)
        botocore.exceptions.ClientError: An error occurred (502) when calling the ListObjects operation (reached max retries: 4): Bad Gateway

        Traceback (most recent call last):
        File "/tmp/tmp.Y6yw1zeeji/ephemeral_component.py", line 131, in train_export_model
            mm_sdk.upload_metrics(data, trainingjobName, version)
        File "/usr/local/lib/python3.8/site-packages/modelmetricsdk/model_metrics_sdk.py", line 176, in upload_metrics
            raise SdkException(str(err)) from None
        modelmetricsdk.sdk_exception.SdkException: An error occurred (502) when calling the ListObjects operation (reached max retries: 4): Bad Gateway

        During handling of the above exception, another exception occurred:

        Traceback (most recent call last):
        File "/usr/local/lib/python3.8/runpy.py", line 194, in _run_module_as_main
            return _run_code(code, main_globals, None,
        File "/usr/local/lib/python3.8/runpy.py", line 87, in _run_code
            exec(code, run_globals)
        File "/usr/local/lib/python3.8/site-packages/kfp/dsl/executor_main.py", line 109, in <module>
            executor_main()
        File "/usr/local/lib/python3.8/site-packages/kfp/dsl/executor_main.py", line 101, in executor_main
            output_file = executor.execute()
        File "/usr/local/lib/python3.8/site-packages/kfp/dsl/executor.py", line 368, in execute
            result = self._func(**func_kwargs)
        File "/tmp/tmp.Y6yw1zeeji/ephemeral_component.py", line 137, in train_export_model
            print(traceback.format_exc())
        NameError: name 'traceback' is not defined
        Error in train_export_model: An error occurred (502) when calling the ListObjects operation (reached max retries: 4): Bad Gateway
        Traceback:
        I0827 05:56:23.172306      46 launcher_v2.go:151] publish success.
        F0827 05:56:23.172347      46 main.go:49] failed to execute component: exit status 1
        time="2024-08-27T05:56:23.387Z" level=info msg="sub-process exited" argo=true error="<nil>"
        Error: exit status 1
        time="2024-08-27T05:56:24.355Z" level=info msg="sub-process exited" argo=true error="<nil>"
        Error: exit status 1
        ```
    - 총 3가지 traceback이 오류로 생성됬음을 확인할 수 있다.
    - botocore이 뭔지 검색해보니 AWS에서 저장소 역할을 하는 S3와 관련이 있는데, 해당 프로젝트에서는 S3 대신 leofs를 사용하는 걸 알 수 있고, 현재 kubefow와 leofs 사이의 네트워크 문제가 발생한 것으로 짐작할 수 있다.
    - ` kubectl edit -n kubeflow configmap/workflow-controller-configmap` 에서 kubeflow의 endpoint를 확인해보자.
        - endpoint: leofs.kubeflow:8080
        - 이걸 보니까 아까 ml-pipline-ui를 8080포트로 연결해놓은게 문득 생각났다....
        - 제거하고 한 번 돌려보자.
        - 아쉽게 이것 때문에 그런건 아닌거 같다...


4. sidecar proxy 주입으로 인한 kfadator, leofs 문제 발생
    - sidcar proxy 주입으로 kfadator에서 문제가 발생했고, 동일한 문제로 leofs까지 연결이 불통이다.

    1. kfadaptor 문제
        ```bash
        2024-08-27 12:44:31,576 | kfadapter_util.py 129 wait_status_thread() |  DEBUG | http://tm.traininghost:32002/trainingjob/pipelineNotification
        2024-08-27 12:44:31,752 | kfadapter_util.py 133 wait_status_thread() |  INFO | <bound method Response.json of <Response [500]>>
        ```
        - 두 번째 라인을 보면 500 즉, 오류가 발생되었고, Response.json에 문제가 있다.
        ```bash
        headers = {'content-type': 'application/json', 'Accept-Charset': 'UTF-8'}
                    try:
                        response = requests.post(url, data=payload, headers=headers)
                        logger.info(response.json)
        ```
        - headers값을 제대로 못받아 오는 것을 확인 할 수 있다...
        
        - `istioctl pc routes -n traininghost kfadapter-848f6dcd47-rghtz -o json` 명령어를 통해 proxy-config 값을 확인할 수 있고, request_headers_to_add, response_headers_to_remove 필드를 확인하면 헤더 규칙을 알 수 있다!!!
        - 조회해봤는데 안나온다... 다른 걸 좀 더 찾아보라는데 내일 찾아보자...