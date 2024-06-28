---
layout: single
title: "4.go structure"
categories: golang
tag: [SDRAN PROJECT]
---


## Go 디렉터리

1. `/cmd`
    - 프로젝트의 메인 애플리케이션들
    - 각 application의 directory name = 만들고 싶은 실행 파일의 이름 (e.g., /cmd/myapp).
    - `/pkg` : 다른 프로젝트들에서 임포트되고 사용될 것 같은 경우
    - `/internal` : 다른사람들이 재사용하지 않기를 바라는 경우
    - `/internal`와 `/pkg` 디렉터리 코드를 임포트, 호출만 하는 작은 `main` 함수는 흔히 볼 수 있습니다.

2. `/pkg`
    - 외부 애플리케이션에서 사용되어도 괜찮은 라이브러리 코드
    - `/pkg` : 그 디렉터리 안의 코드가 다른 사람들에 의해 사용되어도 안전하다고 명시적으로 보여주는 방법
 

## 웹 애플리케이션 디렉터리

1. `/build`
    - 패키징과 지속적인 통합(CI).
    - `/build/package` : 클라우드 (AMI), 컨테이너 (Docker), OS (deb, rpm, pkg) 패키지 설정과 스크립트
    - `/build/ci` : CI (travis, circle, drone) 설정과 스크립트

2. `/tests`
    - 추가적인 외부 테스트 앱들과 테스트 데이터들
    - GO가 무시하게 하는 방법
        - `/test/data`, `/test/testdata` 안에 생성하는 경우
        - 파일 이름 및 디렉토리 이름을 "." 나 "_" 로 시작하는 경우

