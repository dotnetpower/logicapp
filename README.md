# Azure Logic App
Azure Logic App은 코드가 거의 없는(Low Code) 또는 전혀 없는 (No Code) 자동화된 워크플로를 만들고 실행 할 수 있는 클라우드 플랫폼입니다.

# 목차
- 

# Azure 주요 통합 도구


| 제품 | 기능 |
| --- | --- | 
| [Logic Apps](https://azure.microsoft.com/ko-kr/services/logic-apps/) | 클라우드 및 온-프레미스에서 수백 개의 서비스를 연결하기 위해 워크플로 만들기 및 비즈니스 프로세스 오케스트레이션 | 
| [Azure Functions](https://azure.microsoft.com/ko-kr/services/functions/) | 이벤트 기반 서버리스 컴퓨팅 플랫폼을 사용하여 복잡한 오케스트레이션 문제 간소화, 간결한 코드로 트리거 및 바인딩 정의
| [Event Grid](https://azure.microsoft.com/ko-kr/services/event-grid/) | 완전 관리형 이벤트 라우팅 서비스를 사용하여 [지원되는 리소스](https://learn.microsoft.com/ko-kr/azure/event-grid/system-topics)를 이벤트 기반의 게시-구독 모델로 제공 |

기타 Service Bus, API Management, Azure Data Factory 가 있음.

# Logic Apps의 호스팅 옵션
![](images/2024-10-31-10-38-26.png)
Consumption 과 Standard 타입으로 구분되며 Consumption 은 Public Cloud 전용이고, 다중 테넌트 지원 가능.
Workflow Service 는 공용으로 사용되는 환경에서 워크플로를 할당하여 합리적 가격으로 사용 가능, App Service Environment 는 격리된 VM 이 할당.

# 빌딩 블록
로지앱을 통해 빌드되는 워크플로의 구성요소로써 크게 3가지 요소(트리거, 액션, 설정)가 사용됨.

## 1. Triggers(트리거)
모든 워크플로에는 워크플로를 인스턴스화하고 시작하는 호출을 정의하는 트리거가 포함됨.

### 기본 제공 트리거
| 형식 | 설명 |
|--- | --- |
| Request(요청) | 수동 트리거 라고도 하며 다른 리소스가 로직앱을 호출 할 수 있도록 엔드포인트를 생성하여 다른 리소스로 부터 호출을 받아서 실행 |
| Recurrence(되풀이) | 특정 날짜나 시간 조건마다 반복 |
| Event(이벤트), HTTP, HTTPWebhook | HTTP 트리거라고 하며 특정 엔드포인트를 주기적으로 호출을 하는 형태로써 `Request(요청)` 트리거와는 반대 개념 |

### 관리되는 API 트리거
| 형식 | 설명 |
|--- | --- |
| APIConnection | 관려되는 API 커넥터를 사용하여 엔드포인트를 확인하거나 풀링 |
| API ConnectionWebhook | 관리되는 API 커넥터를 구독 하여 이벤트 발생시 실행 |

예) Defender 에서 Alert 이 발생하는 경우, Teams 에서 새로운 메시지를 수신하는 경우 등.

- 커넥터: 트리거에서 사용되거나 후속작업에서 사용되는 작업
    - [Azure Logic Apps의 기본 제공 커넥터](https://learn.microsoft.com/ko-kr/azure/connectors/built-in)
    - [Azure Logic Apps의 관리형 커넥터](https://learn.microsoft.com/ko-kr/azure/connectors/managed)


## 2. Actions(작업)
트리거 후속 작업들로 전체 워크플로에서 각 노드를 정의

### Control
If, Switch, For, do-until 등과 같이 제어를 하기 위한 작업
예시) 트리거가 실행된 결과에 어떤 문자열이 포함되어 있는 경우에는 Condition 이 사용될 수 있고, 임계값을 여러 단계로 설정한 경우에는 Switch 를 이용할 수 있음.
![](images/2024-10-31-11-12-44.png)

### Data Operations
ParseJSON, Join 등과 같이 데이터를 처리, 가공하기 위한 작업
데이터 가공하여 전체 워크플로를 단순화 가능
![](images/2024-10-31-11-16-19.png)

### Variables
변수설정과 관련된 작업으로 변수를 선언(initalize) 하거나 값 할당(Set variable), 값 변경(Increment, Append) 작업
![](images/2024-10-31-11-20-22.png)

### Inline Code
자바스크립트 코드에 익숙한 경우 복잡한 로직을 단순화 하기 위해 인라인 자바스크립트 코드를 사용하여 워크를로 작성에 도움을 줌. 
![](images/2024-10-31-11-22-05.png)
![](images/2024-10-31-11-23-43.png)

### Connectors
빌트인 커넥터 또는 사용자 정의 커넥터를 호출하여 필요한 작업을 실행
예시) 아웃룩을 통해 이메일 발송, Slack 의 특정 채널에 메시지 작성
![](images/2024-10-31-11-24-40.png)

## 3. Configurations(설정)
테스트, 예외처리, 정책 적용 등 워크플로 수행에 필요한 각종 구성 설정

### Run After
변수, 제어문과 같이 특정 제어문이 수행되고 난 이후 예외가 발생되는지 확인 및 조건이 맞는 경우에만 다음 Action(작업)이 실행될 수 있도록 제어 장치(_작업에 마우스 우클릭 시 메뉴로 노출_)

![](images/2024-10-31-12-56-51.png)

- Is successful: 이전 작업이 성공한 경우에만 다음 작업이 실행
- Has timed out: 이전 작업이 타임아웃된 경우에만 다음 작업이 실행
- Is skipped: 이전 작업이 스킵된 경우에만 다음 작업이 실행
- Failed: 이전 작업이 실패한 경우에만 다음 작업이 실행

### Testing
정적 결과로써 작업이 정상적으로 수행되는지 테스트 하기 위한 테스트 로직 (_테스트 로직이 활성화된 상태인 경우 작업의 우측하단에 비이커 모양의 아이콘이 활성화_)
![](images/2024-10-31-13-10-27.png)

### Secure I/O
특정 작업이 보호되어야 할 값이 있는 경우 Security 의 Secure inputs, Secure outputs 을 활성화 하여 실행 결과을 숨길 수 있음. 
또한 구성 설정의 Workflow settings 메뉴에서 허용된 IP 만 접근 허용 설정을 통해 로직앱 접속 [제한](https://learn.microsoft.com/ko-kr/azure/logic-apps/logic-apps-securing-a-logic-app?tabs=azure-portal) 가능
![](images/2024-10-31-13-16-06.png)


# 학습
- [Azure Logic Apps에서 통합 워크플로를 사용하여 비즈니스 프로세스 자동화](https://learn.microsoft.com/ko-kr/training/paths/build-workflows-with-logic-apps/)

# Hands-on

## Lab 1. 기본 워크플로우 생성 및 트리거 설정

### 목표
- Azure Logic Apps에서 워크플로우를 만드는 방법 이해
- 트리거 설정 및 조건을 활용하여 다양한 이벤트 기반 워크플로우 설정

### 사전 준비 사항
- Azure 계정: Azure Portal에 로그인할 수 있어야 합니다.
- Azure 구독: Logic App을 만들기 위한 구독이 필요합니다.
- Azure Resource Group: 모든 리소스를 관리하기 위해 Resource Group을 생성해둡니다. 실습 일관성을 위해 리소스 그룹은 다음과 같은 이름 규칙으로 생성합니다. **rg-handson-lab00-alias**, 예시) rg-handson-lab01-moonchoi

> [!NOTE]  
> Consumption 타입으로 리소스를 생성하면 생성된 리소스가 하나의 워크플로이며, Standard 형태로 생성된 Logic App 은 각 워크플로를 생성한 후 워크플로를 생성한 후 진행 필요.

### Lab 1.1: Logic App 만들기
1. Azure Portal에 로그인하고, Logic App 서비스를 검색하여 클릭합니다.
2. Logic App Designer에서 새 Logic App을 생성합니다.
    - 리소스 이름: lga-lab01-<alias>
    - 리소스 그룹: 기존 리소스 그룹 선택 또는 새로 생성
    - 리전: KoreaCentral
3. 검토 후 생성을 클릭하여 Logic App 리소스를 배포합니다.

### Lab 1.2: 트리거 설정하기 (HTTP 요청 트리거)
1. Logic App이 배포되면 Logic App Designer를 엽니다.
2. 트리거 선택 단계에서 When a HTTP request is received를 검색하고 선택합니다.
3. 트리거 URL 생성: 이 트리거를 설정하면 자동으로 URL이 생성됩니다. 이 URL로 요청을 보내면 워크플로우가 실행됩니다.
    - 요청 본문 예시(use sample payload 를 선택하여 아래 코드 넣으면 스키마 생성)
    ```json
    {
    "name": "John Doe",
    "email": "johndoe@example.com"
    }
    ```
4. `저장`을 눌럭 트리거 설정을 완료합니다.

### Lab 1.3: 조건 추가하여 메시지 생성하기
1. 트리거 아래에 새로운 단계 추가를 클릭합니다.
2. 동작 선택에서 Outlook을 선택하여 이메일을 발송하거나 Slack 또는 Teams와 통합하여 메시지를 보냅니다.
3. 조건 설정:

    - HTTP 요청에서 name이 빈 값이 아닌지 확인하는 조건을 추가합니다.
    - 조건에서 If를 사용하여 조건을 설정합니다.
        - 예: "name" 필드가 "John Doe"일 때 메시지 발송
        ![](images/2024-10-31-15-00-42.png)
4. 메시지 내용 작성:

    - 예시로 "Hello, {name}! Your email is {email}"과 같은 메시지를 작성합니다.
    ![](images/2024-10-31-15-01-12.png)
5. `저장` 후 `개요` 화면으로 이동시 생성된 workflow URL을 확인 가능합니다.

### Lab 1.4: 워크플로우 실행 및 테스트
1. 테스트: 생성된 트리거 URL을 복사합니다.
2. HTTP 요청 보내기:
3. Postman이나 CURL을 사용하여 URL로 JSON 형식의 POST 요청을 보냅니다.
    - 예:
    ```bash
    curl -X POST "<trigger URL>" -H "Content-Type: application/json" -d "{\"name\": \"John Doe\", \"email\": \"johndoe@example.com\"}"

    ```

    - 결과 예시
    ![](images/2024-10-31-15-17-29.png)

    - 수신된 이메일
    ![](images/2024-10-31-15-20-31.png)

4. 결과 확인: Logic Apps의 실행 상태를 확인하여 조건에 맞는 메시지가 전송되었는지 확인합니다.