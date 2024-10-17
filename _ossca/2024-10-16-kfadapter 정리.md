---
layout: single
title: "kfadapter 정리"
categories: open source
tag: [gerrit]
---



## expriment 관련
@APP.route("/experiments/<expname>")
def get_experiment(expname):


@APP.route("/experiments")
def list_experiments():



## run 관련
@APP.route("/runs")
def list_runs():



@APP.route("/runs/<run_id>", methods=['GET', 'DELETE'])
def kf_run(run_id):






## pipeline 관련
@APP.route("/pipelineIds/<pipe_name>", methods=['GET', 'POST'])
def get_pipeline_id(pipe_name):


@APP.route("/pipelines/<pipeline_name>/versions", methods=['GET'])
def get_versions_for_pipeline(pipeline_name):


@APP.route("/pipelines/<pipe_id>", methods=['GET', 'DELETE'])
def get_pipeline(pipe_id):

@APP.route("/pipelines")
def list_pipelines():




## training job 관련
@APP.route('/trainingjobs/<trainingjob_name>/execution', methods=['POST'])
def run_pipeline(trainingjob_name):



## 기타
@APP.route("/liveness")
def kf_liveness():










