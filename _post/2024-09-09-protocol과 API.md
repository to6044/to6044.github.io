---
layout: single
title: "protocol과 API"
categories: open source
tag: [gerrit]
---




## API
- API : appilcation programming interface
- 한 프로그램에서 다른 프로그램으로 데이터를 주고 받기 위한 방법
  
            Ex) 식당 메뉴판 = 식당의 API = 식당과 손님이 음식을 주고 받기 위한 방법
                웹툰 목록 = 웹툰 서비스 API = 웹툰서버와 손님이 웹툰을 주고 받기 위한 방법
                즉 API는 메뉴판! 근데 방법은 뭐냐 -> 방법은 다음 GET code로 구현 -> url 요청하는 부분을 coding
- 요청 방식, 요청 자료, 자료 요청에 필요한 추가 정보(id, user 정보)
- REST API는 web에서 사용되는 대표적인 API

            그럼 user는 API 즉 메뉴판을 보고 원하는 무엇을 받기 위한 GET 요청 code를 짜야함
            알보고면 브라우저 url 창에 적히는 모든 것들이 API 요청과 동일함. 근데 이걸 모든 페이지를 들어가기 위해 사용하는 건 아니잖아? 즉, dashboard에서는 이러한 API 요청을 버튼으로 구현해 놓은 것.



- 하드웨어에서 interface는 연결되었을 때 해당 interface에 맞는 역할을 기기들이 수행하게됨 (충전, 파일이동 등등)
- 소프트웨어에서는? 내가 발명하지 않은 서비스를 사용하기 위해서 서비스의 API를 통해 정보를 받아 내 기기에서 사용할 수 있다. 즉, 내가 발명하지 않아도, API를 맞추면 다양한 서비스를 사용할 수 있다는 것.



## Protocol 
- 통신을 위한 규약, 전송되는 데이터의 항목/순서/표현법을 정의