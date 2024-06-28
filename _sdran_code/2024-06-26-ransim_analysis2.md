---
layout: single
title: "9.RANSIM 분석2"
categories: RANSIM
tag: [SDRAN PROJECT]
---



## RAMSIM repository 구성

### go directory
1. `/cmd` : 프로젝트의 메인 애플리케이션들
    - `/honeycomb/honeycomb.go`
    - `/ransim/ransim.go`
    - `/ransim-tests/ransim-test.go`

2. `/pkg` : 외부 애플리케이션에서 사용되어도 괜찮은 라이브러리 코드
    - /api
        - /cells/cells.go
        - /metrics/metrics.go
        - /model/model.go
        - /nodes/nodes.go
        - /routes/routes.go
        - /trafficsim
            - /trafficsim.go
            - /trafficsim-test.go
        - /ues/ues.go

    - /controller/connection
        - /controller.go
        - /watcher.go

    - /e2agent 
        - /addressing/addressing.go
        - /agents/agents.go
        - /connection
            - /connection.go
            - /options.go
            - /utils.go
        - /agent.go
    
    - /handover
        - /a3handover.go
        - /handover.go

    - /manager/manager.go
    
    - /measurement
        - /converter.go
        - /event3a.go
        - /measurement.go

    - /mobility
        - /diver_test.go
        - /driver.go
        - /routes.go
        - /rrc.go
        - /signal.go

    - /model
        - /layout.go
        - /load.go
        - /mdoel_test.go
        - /model.go

    - /servicemodel
        - /servicemodel.go
        - /kpm2
            - /datasets/cell.csv
            - /defs.go
            - /kpm.go
            - /util.go
        - /mho
            - /a3.go
            - /defs.go
            - /inication.go
            - /mho.go
            - /periodic.go
            - /rrc.go
            - /util.go
        - /rc
            - /v1
                - /defs.go
                - /rc.go
                - /utils.go
            - /defs.go
            - /rc.go
            - /util.go
        - /registry
            - /id.go
            - /registry_test.go
            - /registry.go

    - /store
        - /agents/store.go
        - /cells
            - /cells_test.go
            - /cells.go
            - /types.go
            - /utils.go
        - /conections
            - /connections.go
            - /types.go
        - /event/event.go
        - /metrics
            - /metrics_test.go
            - /metrics.go
            - /types.go
        - /nodes
            - /nodes_test.go 
            - /metrics.go
            - /types.go
        - /routes
            - /routes_test.go
            - /routes.go
            - /types.go
        - /subscriptions
            - /store_test.go
            - /store.go
        - /ues
            - /types.go
            - /ues_test.go
            - ues.go
        - /watcher/watcher.go

    - /utils
        - /e2ap
            - /configupdate/configupdate.go
            - /connectionupdate
                - /connectionSetupFailedItemie/connectionSetupFailedItemie.go
                - /connectionUpdateitemie/connectionUpdateitemie.go
                - /connectionUpdateitemie.go
            - /control
                - /control.go
                - /getters.go
            - /indication/indication.go
            - /indicationerror/indication_error.go
            - /setup/setup.go
            - /subscripption
                - /getters.go
                - /subscription.go
            - /subscriptiondelete
                - /getters.go
                - /subscriptiondelete.go
        - /e2sm
            - /kpm2
                - /id
                    - /cellglobalid/cell_global_id.go
                    - /gnbid/gnb_id.go
                - /indication
                    - /messageformat1/indication_message_format1.go
                    - /messageformat2/indication_message_format2.go
                    - /indication_header.go
                - /labelinfo
                    - /labelinfo_test.go
                    - /labelinfo.go
                - /measobectitem/cell_mess_object_item.go
                - /measurements
                    - /mean_info_action_item.go
                    - /meas_info_item.go
                    - /measurement_data_item
                    - /measurement_item.go
                    - /measurement_type.go
                - /nodeitem/node_item.go
                - /ranfuncdescription/ranfuncdescription.go
                - /reportstyle/report_style_item.go

            - /mho
                - /indication
                    - /header
                        - /indication_header_test.go
                        - /indication_header.go
                    - /message_format1
                        - /indication_message_test.go
                        - /indication_message.go
                    - /message_format2
                        - /indication_message_test.go
                        - /indication_message.go
                - /ranfundesc/ranfundesc.go

            - /rc
                - /controloutcome/control_outcome.go
                - /indication
                    - /header
                        - /indication_header_test.go
                        - /indication_header.go
                    - /message
                        - /indication_message_test.go
                        - /indication_message.go

                - /nrt/nrt_message.go

                - /ranfundesc/nrt_message.go

                - /v1/indication
                    - /headers
                        - /format1/format1.go
                        - /format2/format2.go
                    - /messages
                        - /format3/format3.go
                        - /format5/format5.go
        - /f1ap/fl-messages.go
        - /honeycomb
            - /honeycomb.go
            - /topo.go
        - /measurement
            - /qoffsetrange.go
            - /tttrange.go
        - /xnap/xn-messages.go
        - /geo.go
        - /gnb_id_type.go
        - /ncell_id_type.go
        - /utils_test.go
        - /utils.go



## 외부 application directory

1. `/build` : 패키징과 지속적인 통합(CI) - pakage 설정과 script

2. `/tests` : 추가적인 외부 테스트 앱들과 테스트 데이터들
