---
layout: single
title: "kubeflow adapter repository 기여_1"
categories: open source
tag: [gerrit]
---

                kubeflow adapter는 tm과 kubeflow 사이의 중간 다리 역할을 하는 repository이다. 
                현재 kubeflow에서 pipeline에 관련된 정보를 많이 받아오지 않기 때문에 이를 수정하는 것이 첫 commit의 목표이다.
                먼저 kfadapter는 어떻게 구성되어있고 작동하는지 파악하기 위해 다음과 같이 정리해보았다. 
                ㅈAPI 관련 code도 생소하여 이에 대해서도 알아보았다


## get_pipeline_id
```python
@APP.route("/pipelineIds/<pipe_name>", methods=['GET', 'POST'])
```
- 사용자가 endpoint("/pipelineIds/<pipe_name>")로 GET or POST 요청을 보내면 flask가 해당 함수를 호출하여 요청을 처리함을 의미.
- 입력 : pipe_name
- 반환 : JSON 형식의 dictionary, HTTP 상태 code


1. GET 요청
    - Kubeflow에서 특정 pipeline id를 가져오는데 사용
    ```python
        pipe_id = KFCONNECT_KF_OBJ.get_kf_pipeline_id(pipe_name)
    ```
    - dictionary에 값 저장
    ```python
        pipe_dict['name'] = pipe_name
        pipe_dict['id'] = pipe_id
    ```

2. POST 요청
    - 업로드된 파일 처리를 요청함
    ```python
        uploaded_file = request.files['file']
    ```
    - 업로드된 파일을 /tmp/에 저장
    ```python
        if uploaded_file.filename != '':           #request.files의 filename을 확인하는 것. 
            uploaded_file_path = "/tmp/" + uploaded_file.filename + ".yaml"
            uploaded_file.save(uploaded_file_path)
    ```
    - 업로드된 파일과 description을 활용하여 새 pipeline을 kubeflow에 업로드
    ```python
        description = request.form['description']
        pipe_info = KFCONNECT_KF_OBJ.upload_pipeline_with_versions(
            pipe_name,
            uploaded_file_path,
            description)
    ```

3. 예외 처리
    - ValueError : pipeline 이름이 존재하지 않을 때
    - ApiException : kubeflow API에서 예외가 발생할 때
    - 일반적인 예외 처리

4. GET or POST 후 pipeline name과 id를 JSON 형식의 dictionary로 반환
    ```python
        return jsonify(pipe_dict), status.HTTP_200_OK
    ```



## get_version_for_pipeline
```python
@APP.route("/pipelines/<pipeline_name>/versions", methods=['GET'])
```
- endpoint : "/pipelines/<pipeline_name>/versions"
- 입력 : pipeline_name
- 반환 : JSON 형식의 dictionary, HTTP 상태 code

1. GET 요청
    - Kubeflow에서 특정 pipeline version를 가져오는데 사용
    ```python
        versions_list = KFCONNECT_KF_OBJ.get_pl_versions_by_pl_name(pipeline_name)
    ```
    - dictionary에 값 저장
    ```python
        pipe_dict['name'] = pipe_name
        pipe_dict['id'] = pipe_id
    ```

2. 반환
    - 받아온 dictionary를 version_list에 저장하고 반환
    ```python
        result_dict['versions_list'] = versions_list
        return jsonify(result_dict), status.HTTP_200_OK
    ```




## get_pipeline
```python
@APP.route("/pipelines/<pipe_id>", methods=['GET', 'DELETE'])
```
- endpoint : "/pipelines/<pipe_id>"
- 입력 : pipe_id
- 반환 : JSON 형식의 dictionary, HTTP 상태 code

1. DELETE 요청
    - pipe_id를 기반으로 pipeline을 삭제 후 상태 저장
    ```python
        pipeline_info = KFCONNECT_KF_OBJ.delete_kf_pipeline(pipe_id)
        pipeline_info = {}
        if bool(pipeline_info) is False:
            pipe_dict['id'] = pipe_id
            pipe_dict['status'] = "Deleted"
    ```

2. GET 요청
    - pipe_id 기반으로 piepline 설명과 매개변수를 포함한 pipeline 정보를 Kubeflow에서 가져옴
    ```python
        else:
            pipeline_info = KFCONNECT_KF_OBJ.get_kf_pipeline_desc(pipe_id)
            LOGGER.debug(pipeline_info)
            for parameter in pipeline_info.parameters:
                pipe_arg[parameter.name] = parameter.value              
            pipe_dict['arguments'] = pipe_arg                           
            pipe_dict['description'] = pipeline_info.description       
            pipe_dict['id'] = pipeline_info.id
            pipe_dict['name'] = pipeline_info.name
    ```
    - pipeline 정보
        - pipe_arg : parameter
        - pipe_dict : arguments, description, id, name

3. 반환
    - pipe_dict 반환
    ```python
    return jsonify(pipe_dict), status.HTTP_200_OK
    ```



## kfp.client method 정리
1. pipeline create or upload
    - `create_run_from_pipeline_func(pipeline_func)`
        - Kubernetes 클러스터에서 파이프라인을 실행
    - `create_run_from_pipeline_package(pipeline_file)`
        - Kubernetes 클러스터에서 파이프라인 패키지를 사용하여 파이프라인을 실행.
    - upload : `upload_pipeline(pipeline_package_path[, ...])`  
    - new version upload: `upload_pipeline_version(...[, pipeline_id, ...])`

2. pipeline 정보 조회
    - pipeline id
        - `get_pipeline_id(name)` 있음
    - pipeline info
        - `get_pipeline(pipeline_id)` 있음 
        - V2beta1Pipeline object 반환
        - 
    - pipeline 목록
        - `list_pipelines([page_token, page_size, ...])` 있음
    - pipeline info (version)
        - `get_pipeline_version(pipeline_id, ...)`
    - pipeline version list
        - `list_pipeline_versions(pipeline_id[, ...])` 있음

3. pipeline 삭제
    - pipeline delete
        - `delete_pipeline(pipeline_id)`  있음
    - pipeline delete (version)
        - `delete_pipeline_version(pipeline_id, ...)`







## KFCONNECT_KF_OBJ
- kfadapter __main__ 함수에 가면 KFCONNECT_KF_OBJ = KfConnect()
- KfConnect에서 pipeline 관련해서 어떻게 구현하고 있는지 보자

1. pipe_id = KFCONNECT_KF_OBJ.get_kf_pipeline_id(pipe_name)
```python
def get_kf_pipeline_id(self, pipeline_name):
    pipe_id = self.kfp_client.get_pipeline_id(pipeline_name)
    return pipe_id
```


2. pipe_info = KFCONNECT_KF_OBJ.upload_pipeline_with_versions(pipe_name, uploaded_file_path, description)
    - pipeline upload
    - pi
```python
def upload_pipeline_with_versions(self, pipeline_name, file, desc):
    versions_list = self.get_pl_versions_by_pl_name(pipeline_name)
    length = len(versions_list)
    pipe_info = None
    if length == 0:
        pipe_info = self.kfp_client.upload_pipeline(file, pipeline_name=pipeline_name, description=desc)
    else:
        pipe_info = self.kfp_client.upload_pipeline_version(file, pipeline_version_name=str(length+1),pipeline_name=pipeline_name)
    return pipe_info
```


3. versions_list = KFCONNECT_KF_OBJ.get_pl_versions_by_pl_name(pipeline_name)
```python
def get_pl_versions_by_pl_name(self, pipeline_name):
    pipeline_id = self.get_kf_pipeline_id(pipeline_name)
    if pipeline_id == None:
        return []
    res_obj = self.kfp_client.list_pipeline_versions(pipeline_id, page_size=1000000000)
    if res_obj.total_size is None:
        return []
    obj_list = res_obj.pipeline_versions
    versions_list = []
    for obj in obj_list:
        versions_list.append(obj.display_name)
    return versions_list
```


4. pipeline_info = KFCONNECT_KF_OBJ.get_kf_pipeline_desc(pipe_id)
```
def get_kf_pipeline_desc(self, pipeline_id):
    pipeline = self.kfp_client.get_pipeline(pipeline_id)
    return pipeline
```


5. pipeline_info = KFCONNECT_KF_OBJ.delete_kf_pipeline(pipe_id)
```python
def delete_kf_pipeline(self, pipeline_id):

    pipeline = self.kfp_client.delete_pipeline(pipeline_id)
    return pipeline
```




## 4번의 연장

```python
    pipeline_info = KFCONNECT_KF_OBJ.get_kf_pipeline_desc(pipe_id)   
    # get_kf_pipeline_desc = get_pipeline(pipeline_id), return V2betaPipeline object
    LOGGER.debug(pipeline_info)
    for parameter in pipeline_info.parameters:
        pipe_arg[parameter.name] = parameter.value
    pipe_dict['arguments'] = pipe_arg
    pipe_dict['description'] = pipeline_info.description
    pipe_dict['id'] = pipeline_info.id
    pipe_dict['name'] = pipeline_info.name
```

