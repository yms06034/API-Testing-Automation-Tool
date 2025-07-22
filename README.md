# QtAA - API Testing Automation Tool

  > PyQt5 기반 Postman 대체 API 자동화 테스트 도구

  ## 프로젝트 개요

  본 프로젝트는 회사 내부 규정에 따라 소스 코드는 공개할 수 없으며, 프로젝트 설명만을 포함합니다.

  ### 프로젝트 기간
  - 2025년 3월 ~ 2025년 7월 (5개월)

  ### 개발 인원
  - 1명

  ## 프로젝트 목표

  - Postman의 핵심 기능을 데스크톱 애플리케이션으로 구현
  - JavaScript 런타임 환경을 Python에 임베딩하여 스크립트 실행 지원
  - 대량의 API 테스트를 자동화하고 결과를 체계적으로 관리

  ## 기술 스택

  ### Core Technologies
  - **Language**: Python 3.10+
  - **GUI Framework**: PyQt5 (Qt Designer 활용)
  - **JavaScript Engine**: V8 (py-mini-racer 라이브러리)
  - **Database**: SQLite
  - **Concurrency**: QThread

  ### Architecture
  - **Design Pattern**: MVC 패턴
  - **Communication**: Qt Signal-Slot 메커니즘
  - **Data Management**: 커스텀 ORM-like 인터페이스

  ## 주요 기능

  ### 1. API Request Builder
  - HTTP 메서드 지원 (GET, POST, PUT, DELETE, PATCH)
  - Headers, Body, Query Parameters 설정
  - Form-data, x-www-form-urlencoded, raw JSON 지원
  - 요청 히스토리 자동 저장 및 재사용

  ### 2. JavaScript Runtime Environment
  - **Pre-request Script**: API 호출 전 실행되는 스크립트
    - 동적 변수 설정
    - 인증 토큰 자동 갱신
    - 요청 데이터 전처리

  - **Tests Script**: API 응답 후 실행되는 스크립트
    - 응답 검증 (Status Code, Response Time)
    - JSON 데이터 추출 및 변수 저장
    - 체이닝을 위한 데이터 파싱

  ### 3. Environment & Variables
  - 환경별 변수 관리 (Development, Staging, Production)
  - Global Variables
  - Collection Variables
  - 변수 우선순위: Local > Environment > Global

  ### 4. Collection Runner
  - 여러 API를 순차적으로 실행
  - 변수 공유를 통한 API 체이닝
  - 실행 결과 리포트 생성
  - 반복 실행 및 지연 시간 설정

  ### 5. Data Import/Export
  - Excel/CSV 파일에서 테스트 데이터 일괄 임포트
  - 파라미터 조합을 통한 URL 자동 생성
  - 테스트 결과 Excel 내보내기

  ### 6. Advanced Features
  - 조건부 파라미터 설정
  - URL 패턴 기반 자동 생성
  - 정규식을 활용한 응답 데이터 추출
  - 프로젝트별 데이터 격리

  ## 핵심 구현 사항

  ### 1. V8 JavaScript Engine Integration
  py-mini-racer를 활용하여 V8 엔진을 Python에 임베딩
  Postman의 pm 객체 API를 Python으로 완전 구현
  - pm.environment
  - pm.globals
  - pm.variables
  - pm.request
  - pm.response
  - pm.test()
  - pm.expect() with chai.js style assertions

  ### 2. Asynchronous Processing with QThread
  API 요청 중 UI 블로킹 방지
  pyqtSignal을 통한 안전한 스레드 간 통신
  Progress 업데이트 및 취소 기능 구현
  에러 핸들링 및 재시도 로직

  ### 3. Database Schema Design
  Projects
  ├── Collections
  │   ├── Requests
  │   └── Tests
  ├── Environments
  │   └── Variables
  ├── History
  └── TestResults

  ### 4. Custom UI Components
  - 코드 에디터 with 구문 강조
  - 파라미터 동적 추가/삭제 위젯
  - 체크 가능한 리스트 위젯
  - 커스텀 히스토리 뷰어

  ## 프로젝트 성과

  ### 기술적 성과
  - JavaScript 런타임을 Python 애플리케이션에 성공적으로 통합
  - 383줄의 코드로 Postman pm 객체의 핵심 기능 구현
  - QThread를 활용한 안정적인 비동기 처리 구현
  - MVC 패턴 적용으로 유지보수성 향상

  ### 비즈니스 성과
  - API 테스트 자동화로 테스트 시간 80% 단축
  - Excel 임포트 기능으로 대량 테스트 데이터 처리 가능
  - 팀 내 API 테스트 표준화 달성

  ## 기술적 챌린지 및 해결

  ### 1. JavaScript-Python 데이터 교환
  **문제**: V8 엔진과 Python 간 복잡한 객체 전달 시 직렬화 이슈
  **해결**: JSON 직렬화/역직렬화 레이어 구현 및 순환 참조 감지 로직 추가

  ### 2. 대량 API 호출 시 메모리 누수
  **문제**: Collection Runner에서 수백 개 API 실행 시 메모리 증가
  **해결**: 요청별 컨텍스트 격리 및 명시적 가비지 컬렉션 구현

  ### 3. UI 응답성 유지
  **문제**: 무거운 JavaScript 실행 시 UI 프리징
  **해결**: QThread 풀 구현 및 작업 큐 관리 시스템 도입

  ## 트러블 슈팅
  ### 1. V8 엔진 메모리 누수 문제
  - 상황: Collection Runner에서 100개 이상의 API를 연속 실행 시 메모리 사용량이 지속적으로 증가
    
  #### 원인 분석:
  - py-mini-racer의 Context 객체가 명시적으로 해제되지 않음
  - JavaScript 실행 컨텍스트가 Python 객체를 참조하여 순환 참조
  
  #### 해결 방법:
  **Context를 명시적으로 관리하고 사용 후 즉시 해제**
  with MiniRacer() as ctx:
      ctx.eval(script)
  __exit__ 시점에 자동으로 메모리 해제

  # 추가로 주기적인 가비지 컬렉션 수행
  import gc
  gc.collect()

  결과: 메모리 사용량 70% 감소, 1000개 이상의 API 연속 실행 가능

  ### 2. 전역 변수 동기화
  - 문제: JavaScript의 전역 변수 변경을 Python에 반영하는 방법
    
  - 해결:
  ```globals: {
      values: globals,
      get: function (key) { return this.values[key]; },
      set: function (key, value) {
          this.values[key] = value;
          // Python에 변경 알림
          console.log("__globals_update__" + JSON.stringify({key: key, value: value}));
      }
  }
  ```

  **Python에서 전역 변수 업데이트 감지**
  ```
  updated_globals = ctx.eval("JSON.stringify(globals)")
  globals_dict.clear()
  globals_dict.update(json.loads(updated_globals))
  ```

  ## 한계점과 아쉬움
  
  ### 1. 로컬 실행의 근본적 한계

  #### 리소스 모니터링 코드
  ```
  def check_system_resources():
      cpu_percent = psutil.cpu_percent(interval=0.1)
      memory = psutil.virtual_memory()
      if cpu_percent > 80:
          return False, "CPU usage too high"
      if memory.percent > 90:
          return False, "Memory critical"
      return True, "OK"
  ```

  사용자 PC 성능에 전적으로 의존하여, 저사양 PC에서는 대규모 테스트 실행이 어려웠습니다.

  ### 2. CORS 문제
  - Postman은 클라우드 프록시로 CORS를 우회하지만, 로컬에서는 불가능했습니다.

  ### 3. 실시간 협업 불가
  - 파일 기반 동기화의 한계로 실시간 협업이 불가능했습니다.
  

  ## 프로젝트 회고

  ### 잘한 점
  - V8 엔진 통합으로 Postman과 높은 호환성 달성
  - 체계적인 아키텍처 설계로 확장성 확보
  - 사용자 피드백을 반영한 UI/UX 개선

  ### 개선할 점
  - 더 많은 Postman 기능 지원 (GraphQL, WebSocket)
  - 플러그인 시스템 도입으로 확장성 향상
  - 클라우드 동기화 기능 추가

  이 프로젝트를 통해 JavaScript 런타임의 내부 동작을 깊이 이해하게 되었고, 특히 V8 엔진과 Python의 통합이라는 독특한 경험을 쌓을 수 있었습니다. 
  비록 Postman의 모든 기능을 재현하지는 못했지만, 팀의 실질적인 문제는 해결했고, 그것이 진정한 성과였다고 생각합니다.
