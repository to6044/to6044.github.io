---
layout: single
title: "protocol과 API"
categories: open source
tag: [gerrit]
---


## Protocol 
- 통신을 위한 규약, 전송되는 데이터의 항목/순서/표현법을 정의


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



## REST API
API란 두 소프트웨어가 서로 요청과 응답을 주고 받을 때 요청을 보내는 방법, 응답하는 방법에 대해 메뉴판 처럼 정해두는 것이다.   

RESTful API는 API 중 가장 널리 사용되는 API로, HTTP에서 필요한 자원에 접근할 때 웹의 장점을 최대한 활용하기 위한 architecture이다.

- RESTful API특징     


1. url이 '어떤 자원'에 관한 것인지 표현해야하고, 그것만 표현하는 것이 있다. 즉, 무엇을 하는가?에 대한 정보는 url에 포함되지 않아야 하며, 작업의 종류는 HTTP method로 표현된다. 

2. 사용하는 HTTP의 method에는 GET, DELETE, POST, PUT, PATCH가 있다. 먼저 (GET, DELETE), (POST, PUT, PATCH) 두 묶음으로 나눌 수 있는데, 1번 묶음 보다 2번 묶음이 더 많은 내용을 담을 수 있다.    
   
3. method와 uri만 보고도 해당 API가 어떤 기능을 하는지 쉽게 파악할 수 있다는 것이 장점이다.    

4. RESTful API의 요청과 응답에는 JSON이 많이 사용되는데, 이는 구조화된 데이터 표현이 가능하면서도 가볍기 때문이다. 

5. HATEOAS(Hypermedia As The Engine Of Application state)   
각 요청의 응답에 가용한 다른 요청의 정보도 포함시켜 개발자가 따로 찾아보지 않을 수 있게 정보를 첨부함.   


- GET 요청 예시 -> kfadapter_main.py의 @APP.route("/pipelines", methods=['GET'])   
client가 kfadpater 서비스에 GET method를 사하여 http://<cluster ip>:<port>/pipelines 엔드포인트에 요청을 보내면, 해당 서비스에서는 해당 pod 내부에서 이 코드를 실행시켜 얻은 값을 client에 응답으로 보냄. 즉, client가 read하는 것.

- POST 요청   
client가 서비스에 POST method를 사용하여 엔드포인트에 upload 하고 싶은 데이터와 함께 요청을 보내면, 해당 서비스는 해당 pod 내부에서 코드를 실행시켜 데이터를 upload함. 즉 client가 create하는 것. 만약 방금 client가 등록한 정보를 client가 보고 싶다면 GET 요청을 통해 살펴볼 수 있음.


- PUT, PATCH 요청   
두 가지 모두 수정이기 때문에 특정 항목에 대한 정보가 uri에 포함됨. PUT 요청은 특정 항목의 정보를 전체적으로 대체할 때 사용되고, PATCH요청은 정보 중 일부만 수정할 때 사용됨.

- DELTE 요청   
삭제할 항목만 적어주면 되기 때문에 용량이 작으며, 삭제할 항목에 대한 정보가 uri에 포함됨