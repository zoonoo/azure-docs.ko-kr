---
title: "워크플로 작업 및 트리거 - Azure Logic Apps | Microsoft Docs"
description: 
services: logic-apps
author: MandiOhlinger
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/17/2016
ms.author: LADocs; mandia
ms.translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: ff86340f18a2d3d13d55b7e0bcd4122d9b85ccd9
ms.contentlocale: ko-kr
ms.lasthandoff: 04/20/2017

---

# <a name="workflow-actions-and-triggers-for-azure-logic-apps"></a>Azure Logic Apps의 워크플로 작업 및 트리거

논리 앱은 트리거와 작업으로 구성됩니다. 트리거에는 여섯 가지 유형이 있습니다. 각 유형마다 서로 다른 인터페이스 및 동작을 포함합니다. [워크플로 정의 언어](logic-apps-workflow-definition-language.md)의 세부 정보를 확인하여 기타 자세한 내용을 알아볼 수도 있습니다.  
  
트리거 및 작업에 대한 자세한 내용과 이들을 사용하여 논리 앱을 빌드하고 비즈니스 프로세스 및 워크플로를 개선하는 방법에 대해 알아보세요.  
  
### <a name="triggers"></a>트리거  

트리거는 논리 앱 워크플로의 실행을 시작할 수 있는 호출을 지정합니다. 워크플로 실행을 시작하는 두 가지 서로 다른 방법은 다음과 같습니다.  
  
-   폴링 트리거  

-   푸시 트리거 - [워크플로 서비스 REST API](https://docs.microsoft.com/rest/api/logic/workflows) 호출  
  
모든 트리거는 다음과 같은 상위 수준 요소를 포함합니다.  
  
```json
"<name-of-the-trigger>" : {
    "type": "<type-of-trigger>",
    "inputs": { <settings-for-the-call> },
    "recurrence": {  
        "frequency": "Second|Minute|Hour|Week|Month|Year",
        "interval": "<recurrence interval in units of frequency>"
    },
    "conditions": [ <array-of-required-conditions > ],
    "splitOn" : "<property to create runs for>",
    "operationOptions": "<operation options on the trigger>"
}
```

### <a name="trigger-types-and-their-inputs"></a>트리거 유형 및 해당 입력  

다음과 같은 트리거 유형을 사용할 수 있습니다.
  
-   **Request** \- 논리 앱을 사용자가 호출할 끝점으로 만듭니다.  
  
-   **Recurrence** \- 정의된 일정에 따라 실행합니다.  
  
-   **HTTP** \- HTTP 웹 끝점을 폴링합니다. HTTP 끝점은 202\-비동기 패턴을 사용하거나 배열을 반환하여 \- 특정 트리거링 계약을 준수해야 합니다.  
  
-   **ApiConnection** \- HTTP 트리거처럼 폴링하지만 [Microsoft 관리 API](https://docs.microsoft.com/azure/connectors/apis-list)를 활용합니다.  
  
-   **HTTPWebhook** \- 수동 트리거처럼 끝점을 열지만 지정된 URL을 호출하여 등록 및 등록 취소도 가능합니다.  
  
-   **ApiConnectionWebhook** \- Microsoft 관리 API를 활용하여 HTTPWebhook 트리거처럼 작동합니다.       
    각 트리거 형식은 해당 동작을 정의하는 서로 다른 **입력** 집합을 포함합니다.  
  
## <a name="request-trigger"></a>요청 트리거  

이 트리거는 논리 앱을 호출하는 HTTP 요청을 통해 호출하는 끝점으로 사용됩니다. 요청 트리거는 다음 예제와 같이 표시됩니다.  
  
```json
"<name-of-the-trigger>" : {
    "type" : "request",
    "kind": "http",
    "inputs" : {
        "schema" : {
            "properties" : {
                "myInputProperty1" : { "type" : "string" },
                "myInputProperty2" : { "type" : "number" }
            },
        "required" : [ "myInputProperty1" ],
        "type" : "object"
        }
    }
} 
```

또한 **schema**라는 선택적 속성도 있습니다.  
  
|요소 이름|필수|설명|  
|----------------|------------|---------------|  
|schema|아니요|들어오는 요청의 유효성을 검사하는 JSON 스키마입니다. 후속 워크플로 단계에서 참조할 속성을 파악하는 데 유용합니다.|

이 끝점을 호출하려면 *listCallbackUrl* API를 호출해야 합니다. [워크플로 서비스 REST API](https://docs.microsoft.com/rest/api/logic/workflows)를 참조하세요.  
  
## <a name="recurrence-trigger"></a>되풀이 트리거  

되풀이 트리거는 정의된 일정에 따라 실행됩니다. 이러한 트리거는 다음 예제와 같이 표시될 수 있습니다.  

```json
"dailyReport" : {
    "type": "recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": "1"
    }
}
```

보시는 바와 같이 워크플로를 실행하는 간단한 방법입니다.  
  
|요소 이름|필수|설명|  
|----------------|------------|---------------|  
|frequency|예|트리거가 실행되는 빈도입니다. second, minute, hour, day, week, month 또는 year 중에 하나만 사용할 수 있습니다.|  
|interval|예|되풀이에 대해 지정된 빈도 간격|  
|startTime|아니요|UTC 오프셋 없이 startTime이 제공될 경우 이 timeZone이 사용됩니다.|  
|timeZone|no|UTC 오프셋 없이 startTime이 제공될 경우 이 timeZone이 사용됩니다.|  
  
미래의 특정 시점에 실행을 시작하도록 트리거를 예약할 수도 있습니다. 예를 들어 매주 월요일마다 보고서를 시작하려면 다음 트리거를 만들어 월요일마다 시작하도록 논리 앱을 예약할 수 있습니다.  

```json
"dailyReport" : {
    "type": "recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime" : "2015-06-22T00:00:00Z"
    }
}
```

## <a name="http-trigger"></a>HTTP 트리거  

HTTP 트리거는 지정된 끝점을 폴링하고 응답을 확인하여 워크플로를 실행할지 여부를 결정합니다. 입력 개체는 HTTP 호출을 구성하는 데 필요한 매개 변수 집합을 사용합니다.  
  
|요소 이름|필수|설명|형식|  
|----------------|------------|---------------|--------|  
|메서드|yes|다음 HTTP 메서드 중 하나일 수 있습니다. GET, POST, PUT, DELETE, PATCH 또는 HEAD|문자열|  
|uri|yes|호출된 http 또는 https 끝점입니다. 최대 2킬로바이트입니다.|string|  
|쿼리|아니요|URL에 추가할 쿼리 매개 변수를 나타내는 개체입니다. 예를 들어 `"queries" : { "api-version": "2015-02-01" }`은 URL에 `?api-version=2015-02-01`을 추가합니다.|Object|  
|headers|아니요|요청에 전송된 각 헤더를 나타내는 개체입니다. 예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 합니다. `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|Object|  
|body|아니요|끝점에 전송된 페이로드를 나타내는 개체입니다.|Object|  
|retryPolicy|아니요|4xx 또는 5xx 오류에 대한 재시도 동작을 사용자 지정할 수 있는 개체입니다.|Object|  
|authentication|아니요|요청을 인증해야 하는 메서드를 나타냅니다. 이 개체에 대한 자세한 내용은 [스케줄러 아웃바운드 인증](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication)을 참조하세요. 스케줄러 이외에도 지원되는 속성이 하나 더 있습니다. `authority` 기본적으로 이 값은 지정되지 않은 경우 `https://login.windows.net`이지만 `https://login.windows\-ppe.net`과 같이 다른 대상 그룹을 사용할 수 있습니다.|Object|  
  
HTTP 트리거가 논리 앱과 잘 작동하도록 하려면 특정 패턴을 따르는 HTTP API가 필요합니다. 다음 필드가 필요합니다.  
  
|응답|설명|  
|------------|---------------|  
|상태 코드|상태 코드 200 \(OK\)이면 실행이 진행됩니다. 다른 상태 코드이면 실행이 진행되지 않습니다.|  
|Retry\-after 헤더|논리 앱이 끝점을 다시 폴링할 때까지 시간(초) 수입니다.|  
|위치 헤더|다음 폴링 간격에서 호출할 URL입니다. 지정하지 않으면 원래 URL이 사용됩니다.|  
  
다양한 요청 유형의 다양한 동작에 대한 예는 다음과 같습니다.  
  
|응답 코드|Retry\-After|동작|  
|-----------------|----------------|------------|  
|200|\(없음\)|유효한 트리거가 아니므로 Retry\-After가 필요하나, 없는 경우 엔진에서 다음 요청을 폴링하지 않습니다.|  
|202|60|워크플로를 트리거하지 않습니다. 1분 후 다음 시도가 발생합니다.|  
|200|10|워크플로를 실행하고 10초 후 더 많은 내용에 대해 다시 확인합니다.|  
|400|\(없음\)|잘못된 요청이며 워크플로를 실행하지 않습니다. **다시 시도 정책**이 정의되지 않으면 기본 정책이 사용됩니다. 다시 시도 횟수에 도달하면 트리거가 더 이상 유효하지 않습니다.|  
|500|\(없음\)|서버 오류이며 워크플로를 실행하지 않습니다.  **다시 시도 정책**이 정의되지 않으면 기본 정책이 사용됩니다. 다시 시도 횟수에 도달하면 트리거가 더 이상 유효하지 않습니다.|  
  
HTTP 트리거의 출력은 다음 예제와 같습니다.  
  
|요소 이름|설명|형식|  
|----------------|---------------|--------|  
|headers|http 응답의 헤더입니다.|Object|  
|body|http 응답의 본문입니다.|Object|  
  
## <a name="api-connection-trigger"></a>API 연결 트리거  

API 연결 트리거는 기본 기능에서 HTTP 트리거와 유사합니다. 하지만 작업을 식별하는 매개 변수는 다릅니다. 다음은 예제입니다.  
  
```json
"dailyReport" : {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            },
        }
        "connection": {
            "name": "@parameters('$connections')['myconnection'].name"
        }
    },  
    "method": "POST",
    "body": {
        "category": "awesomest"
    }
}
```

|요소 이름|필수|형식|설명|  
|----------------|------------|--------|---------------|  
|host|예||ApiApp 호스트된 게이트웨이 및 ID입니다.|  
|메서드|예|String|다음 HTTP 메서드 중 하나일 수 있습니다. **GET**, **POST**, **PUT**, **DELETE**, **PATCH** 또는 **HEAD**|  
|쿼리|아니요|Object|URL에 추가할 쿼리 매개 변수를 나타냅니다. 예를 들어 `"queries" : { "api-version": "2015-02-01" }`은 URL에 `?api-version=2015-02-01`을 추가합니다.|  
|headers|아니요|Object|요청에 전송된 각 헤더를 나타냅니다. 예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 합니다. `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|아니요|Object|끝점에 전송된 페이로드를 나타냅니다.|  
|retryPolicy|아니요|Object|4xx 또는 5xx 오류에 대한 재시도 동작을 사용자 지정할 수 있습니다.|  
|authentication|아니요|Object|요청을 인증해야 하는 메서드를 나타냅니다. 이 개체에 대한 자세한 내용은 [스케줄러 아웃바운드 인증](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication)을 참조하세요.|  
  
호스트 속성은 다음과 같습니다.  
  
|요소 이름|필수|설명|  
|----------------|------------|---------------|  
|api runtimeUrl|예|관리 API의 끝점입니다.|  
|connection name||`$connection`이라는 매개 변수에 대한 참조여야 하며 워크플로에서 사용하는 관리 API 연결의 이름입니다.|
  
API 연결 트리거의 출력은 다음과 같습니다.
  
|요소 이름|형식|설명|  
|----------------|--------|---------------|  
|headers|Object|http 응답의 헤더입니다.|  
|body|Object|http 응답의 본문입니다.|  
  
## <a name="httpwebhook-trigger"></a>HTTPWebhook 트리거  

HTTPWebhook 트리거는 수동 트리거처럼 끝점을 열지만 지정된 URL을 호출하여 등록 및 등록 취소도 가능합니다. HTTPWebhook 트리거의 예는 다음과 같습니다.  

```json
"myappspottrigger": {
    "type": "httpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "https://pubsubhubbub.appspot.com/subscribe",
            "headers": { },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": { },
            "retryPolicy": { }
        },
        "unsubscribe": {
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "method": "POST",
            "authentication": { }
        }
    },
    "conditions": [ ]
    }
```

대부분의 섹션은 선택 사항이며 웹후크의 동작은 어떤 섹션이 제공 또는 생략되었는지에 따라 달라집니다.  
웹후크의 속성은 다음과 같습니다.  
  
|요소 이름|필수|설명|  
|----------------|------------|---------------|  
|subscribe|아니요|트리거가 만들어지고 초기 등록을 수행할 때 호출되는 나가는 요청입니다.|  
|unsubscribe|아니요|트리거가 삭제될 때 나가는 요청입니다.|  
  
-   **Subscribe**은 이벤트 수신 대기를 시작하기 위해 수행되는 나가는 호출입니다. 이 호출은 일반 HTTP 작업에서 수행할 때와 동일한 매개 변수 집합으로 시작됩니다. 이 나가는 호출은 예를 들어 자격 증명이 롤링되거나 트리거의 입력 매개 변수가 변경될 때마다 등 워크플로가 어떤 식으로든 변경될 때 언제든지 이루어집니다.
  
    이 호출을 지원하기 위해 `@listCallbackUrl()`이라는 새로운 함수가 제공됩니다. 이 함수는 이 워크플로에서 특정 트리거에 대해 고유한 URL을 반환합니다. 서비스 REST를 사용하는 끝점에 대한 고유한 식별자를 나타냅니다.  
  
-   **Unsubscribe**는 다음과 같이 작업에서 이 트리거를 무효화하도록 렌더링할 때 호출됩니다.  
  
    -   트리거 삭제 또는 비활성화  
  
    -   워크플로 삭제 또는 비활성화  
  
    -   구독 삭제 또는 비활성화  
  
    논리 앱에서 구독 취소(unsubscribe) 작업을 자동으로 호출합니다. 이 함수에 대한 매개 변수는 HTTP 트리거와 같습니다.  
  
    HTTPWebhook 트리거에 대한 출력은 들어오는 요청의 콘텐츠입니다.  
  
|요소 이름|형식|설명|  
|-----------------|--------|---------------|  
|headers|Object|http 요청의 헤더입니다.|  
|body|Object|http 요청의 본문입니다.|  

[HTTP 비동기 제한](#asynchronous-limits)과 동일한 방식으로 웹후크 작업에 대한 제한을 지정할 수 있습니다.
  

## <a name="conditions"></a>조건  

모든 트리거에 대해 하나 이상의 조건을 사용하여 워크플로의 실행 여부를 결정할 수 있습니다. 예:  

```json
"dailyReport" : {
    "type": "recurrence",
    "conditions": [ {
        "expression": "@parameters('sendReports')"
    } ],
    "recurrence": {
        "frequency": "Day",
        "interval": "1"
    }
}
```

이 경우 워크플로의 `sendReports` 매개 변수가 true로 설정된 동안만 보고서가 트리거됩니다. 마지막으로 조건에서 트리거의 상태 코드를 참조할 수 있습니다. 예를 들어 다음과 같이 웹 사이트에서 상태 코드 500을 반환하는 경우에만 워크플로를 시작할 수 있습니다.
  
```  
"conditions": [  
        {  
          "expression": "@equals(triggers().code, 'InternalServerError')"  
        }  
      ]  
```  
  
> [!NOTE]  
> 식에서 트리거의 상태 코드를 참조하는 경우\(어떤 식으로든\) 기본 동작\(200 \(OK\)에서만 트리거됨\)이 바뀝니다. 예를 들어 상태 코드 200 및 상태 코드 201 모두에서 트리거하려면 조건으로 `@or(equals(triggers().code, 200),equals(triggers().code,201))`을 포함해야 합니다.  
  
## <a name="start-multiple-runs-for-a-request"></a>요청에 대해 여러 실행 시작

단일 요청에 대해 여러 실행을 시작하려면 `splitOn`이 유용합니다. 예를 들어 폴링 간격 중에 새 항목을 여러 개 포함할 수 있는 끝점을 폴링하려는 경우를 들 수 있습니다.
  
`splitOn`에는 트리거 실행을 시작하는 데 각 항목을 사용할 항목 배열을 포함하는 응답 페이로드 내에 속성을 지정합니다. 예를 들어 다음 응답을 반환하는 API가 있다고 가정해 보겠습니다.  
  
```json
{
    "Status" : "success",
    "Rows" : [
        {  
            "id" : 938109380,
            "name" : "mycoolrow"
        },
        {
            "id" : 938109381,
            "name" : "another row"
        }
    ]
}
```
  
논리 앱에는 Rows 콘텐츠만 필요하므로 다음 예제처럼 트리거를 구성할 수 있습니다.  
  
```json
"mysplitter" : {
    "type" : "http",
    "recurrence": {
        "frequency": "Minute",
        "interval": "1"
    },
    "intputs" : {
        "uri" : "https://mydomain.com/myAPI",
        "method" : "GET"
    },
    "splitOn" : "@triggerBody()?.Rows"
}
```
  
그러면 워크플로 정의에서 `@triggerBody().name`이 첫 번째 실행에 대해 `mycoolrow`를 반환하고 두 번째 실행에 대해 `another row`를 반환합니다. 트리거 출력은 다음 예제와 같이 표시됩니다.  
  
```json
{
    "body" : {
        "id" : 938109381,
        "name" : "another row"
    }
}
```

따라서 `SplitOn`을 사용하는 경우 배열 외부의 속성(이 경우 `Status` 필드)을 가져올 수 없습니다.  
  
> [!NOTE]  
> 이 예에서는 `?` 연산자를 사용하여 `Rows` 속성이 없는 경우 오류를 방지할 수 있습니다. 
  
## <a name="single-run-instance"></a>단일 실행 인스턴스

모든 활성 실행이 완료된 경우에만 실행할 되풀이 속성이 있는 트리거를 구성할 수 있습니다. 진행 중인 실행이 있는 동안 예약된 되풀이가 발생하면 트리거는 이를 건너뛰고 다음 예정된 되풀이 간격까지 기다렸다가 다시 확인합니다.

작업 옵션을 통해 이 설정을 구성할 수 있습니다.

```json
"triggers": {
    "mytrigger": {
        "type": "http",
        "inputs": { ... },
        "recurrence": { ... },
        "operationOptions": "singleInstance"
    }
}
```

## <a name="types-and-inputs"></a>형식 및 입력  

다양한 작업 형식이 있으며 각각 고유한 동작을 포함합니다. 컬렉션 작업은 자체적으로 여러 다른 작업을 포함할 수 있습니다.

### <a name="standard-actions"></a>표준 작업  

-   **HTTP** 이 작업은 HTTP 웹 끝점을 호출합니다.  
  
-   **ApiConnection** \- HTTP 작업처럼 동작하지만 Microsoft 관리 API를 사용합니다.  
  
-   **ApiConnectionWebhook** \- HTTPWebhook와 유사하지만 Microsoft 관리 API를 사용합니다.  
  
-   **Response** \- 들어오는 호출에 대한 응답을 정의합니다.  
  
-   **Wait** \- 정해진 시간 동안 기다리거나 특정 시간까지 대기하는 단순한 작업입니다.  
  
-   **Workflow** \- 중첩된 워크플로를 나타냅니다.  

-   **Function** \- 이 작업은 Azure Function을 나타냅니다.

### <a name="collection-actions"></a>컬렉션 작업

-   **Scope** \- 다른 작업의 논리적 그룹화입니다.

-   **Condition** \- 식을 평가하고 해당하는 결과 분기를 실행합니다.

-   **ForEach** \- 이 루프 작업은 배열을 반복하고 각 항목에 대해 내부 작업을 수행합니다.

-   **Until** \- 이 루프 작업은 조건 결과가 true일 때까지 내부 작업을 실행합니다.
  
각 작업 형식은 작업의 동작을 정의하는 서로 다른 **입력** 집합을 포함합니다.  
  
## <a name="http-action"></a>HTTP 동작  

HTTP 작업은 지정된 끝점을 호출하고 응답을 확인하여 워크플로를 실행할지 여부를 결정합니다. **입력** 개체는 HTTP 호출을 구성하는 데 필요한 매개 변수 집합을 사용합니다.  
  
|요소 이름|필수|형식|설명|  
|----------------|------------|--------|---------------|  
|메서드|예|String|다음 HTTP 메서드 중 하나일 수 있습니다. **GET**, **POST**, **PUT**, **DELETE**, **PATCH** 또는 **HEAD**|  
|uri|예|String|호출된 http 또는 https 끝점입니다. 최대 길이는 2킬로바이트입니다.|  
|쿼리|아니요|Object|URL에 추가할 쿼리 매개 변수를 나타냅니다. 예를 들어 `"queries" : { "api-version": "2015-02-01" }`은 URL에 `?api-version=2015-02-01`을 추가합니다.|  
|headers|아니요|Object|요청에 전송된 각 헤더를 나타냅니다. 예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 합니다. `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|아니요|Object|끝점에 전송된 페이로드를 나타냅니다.|  
|retryPolicy|아니요|Object|4xx 또는 5xx 오류에 대한 재시도 동작을 사용자 지정할 수 있습니다.|  
|operationsOptions|아니요|string|재정의할 특정 동작 집합을 정의합니다.|  
|authentication|아니요|Object|요청을 인증해야 하는 메서드를 나타냅니다. 이 개체에 대한 자세한 내용은 [스케줄러 아웃바운드 인증](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication)을 참조하세요. 스케줄러 이외에도 지원되는 속성이 하나 더 있습니다. `authority` 기본적으로 지정되지 않은 경우 `https://login.windows.net`이지만 `https://login.windows\-ppe.net`과 같이 다른 대상 그룹을 사용할 수 있습니다.|  
  
HTTP 작업 \(및 API 연결\) 작업은 다시 시도 정책을 지원합니다. 다시 시도 정책은 모든 연결 예외 외에도 HTTP 상태 코드 408, 429 및 5xx로 지정되는 일시적인 오류에 적용됩니다. 이 정책은 여기에 표시된 대로 정의된 *retryPolicy* 개체를 사용하여 설명합니다.
  
```json
"retryPolicy" : {
    "type": "<type-of-retry-policy>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```
  
재시도 간격은 ISO 8601 형식으로 지정됩니다. 이 간격에는 기본값이 있으며 최소값은 20초인 반면 최대값은 1시간입니다. 기본값 및 최대 재시도 횟수는 4시간입니다. 다시 시도 정책 정의가 지정되지 않은 경우 기본 재시도 횟수 및 간격 값으로 `fixed` 전략이 사용됩니다. 다시 시도 정책을 사용하지 않도록 설정하려면 해당 형식을 `None`으로 설정합니다.  
  
예를 들어 다음 작업은 일시적인 오류가 있는 경우 최신 뉴스 가져오기를 두 번 다시 시도하는 데 각 시도 사이에 30초 지연을 포함하여 총 3번 실행합니다.  
  
```json
"latestNews" : {
    "type": "http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```
### <a name="asynchronous-patterns"></a>비동기 패턴

기본적으로 모든 HTTP 기반 작업은 표준 비동기 작업 패턴을 지원합니다. 따라서 원격 서버가 202 \(허용됨\) 응답으로 처리를 위해 요청을 수락했다는 것을 나타내면 Logic Apps 엔진은 터미널 상태가 \(비\-202 응답\)에 도달할 때까지 응답의 위치 헤더에 지정된 URL의 폴링을 유지합니다.  
  
이전에 설명된 비동기 동작을 사용하지 않도록 하려면 작업 입력에서 `DisableAsyncPattern` 옵션을 설정합니다. 이 경우 작업의 출력은 서버에서 초기 202 응답을 기반으로 합니다.  
  
```json
"invokeLongRunningOperation" : {
    "type": "http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```

#### <a name="asynchronous-limits"></a>비동기 제한

비동기 패턴의 지속 시간은 특정 시간 간격으로 제한될 수 있습니다.  터미널 상태에 도달하지 않고 시간 간격이 경과하면 작업의 상태가 `ActionTimedOut` 코드로 `Cancelled`로 표시됩니다.  시간 제한은 ISO 8601 형식으로 지정됩니다.  제한은 다음 구문을 사용하여 지정할 수 있습니다.

``` json
"<action-name>": {
    "type": "workflow|webhook|http|apiconnectionwebhook|apiconnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="api-connection"></a>API 연결  

API 연결은 Microsoft 관리 커넥터를 참조하는 작업입니다.
이 작업에는 유효한 연결에 대한 참조와 필요한 API 및 매개 변수에 대한 정보가 필요합니다.

|요소 이름|필수|형식|설명|  
|----------------|------------|--------|---------------|  
|host|예|Object|runtimeUrl 및 연결 개체에 대한 참조 등 커넥터 정보를 나타냅니다.|
|메서드|예|String|다음 HTTP 메서드 중 하나일 수 있습니다. **GET**, **POST**, **PUT**, **DELETE**, **PATCH** 또는 **HEAD**|  
|path|예|문자열|API 작업의 경로입니다.|  
|쿼리|아니요|Object|URL에 추가할 쿼리 매개 변수를 나타냅니다. 예를 들어 `"queries" : { "api-version": "2015-02-01" }`은 URL에 `?api-version=2015-02-01`을 추가합니다.|  
|headers|아니요|Object|요청에 전송된 각 헤더를 나타냅니다. 예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 합니다. `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|아니요|Object|끝점에 전송된 페이로드를 나타냅니다.|  
|retryPolicy|아니요|Object|4xx 또는 5xx 오류에 대한 재시도 동작을 사용자 지정할 수 있습니다.|  
|operationsOptions|아니요|string|재정의할 특정 동작 집합을 정의합니다.|  

```json
"Send_Email": {
    "type": "apiconnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "post",
        "body": {
            "Subject": "New Tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
    }
```

## <a name="api-connection-webhook-action"></a>API 연결 웹후크 작업

```json
"Send_approval_email": {
    "type": "apiconnectionwebhook",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "body": {
            "Message": {
                "Subject": "Approval Request",
                "Options": "Approve, Reject",
                "Importance": "Normal",
                "To": "me@email.com"
            }
        },
        "path": "/approvalmail",
        "authentication": "@parameters('$authentication')"
    },
    "runAfter": {}
}
```

[HTTP 비동기 제한](#asynchronous-limits)과 동일한 방식으로 웹후크 작업에 대한 제한을 지정할 수 있습니다.
  
## <a name="response-action"></a>응답 작업  

이 작업 유형에는 HTTP 요청의 전체 응답 페이로드와 statusCode, body 및 headers가 포함됩니다.  
  
```json
"myresponse" : {
    "type" : "response",
    "inputs" : {
        "statusCode" : 200,
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        }
    },
    "runAfter": {}
}
```
  
응답 작업에는 다른 작업에는 적용되지 않는 특수한 제한이 있습니다. 구체적으로 살펴보면 다음과 같습니다.  
  
-   들어오는 요청에 대해 결정적 응답이 필요하므로 응답 작업은 정의에 병렬일 수 없습니다.  
  
-   들어오는 요청이 응답을 수신한 후 응답 작업에 도달하면 작업은 실패\(충돌\)로 간주되므로 실행은 `Failed`입니다.  
  
-   응답 작업이 있는 워크플로는 한 번의 호출로 여러 개의 실행이 발생하므로 해당 트리거에 `splitOn`을 포함할 수 없습니다. 따라서 흐름이 PUT이면 유효성을 검사해야 하며 잘못된 요청이 발생합니다.  
  
## <a name="wait-action"></a>대기 작업  

`wait` 작업은 지정된 간격에 워크플로 실행을 일시 중단합니다. 예를 들어 15분을 기다리려면 이 코드 조각을 사용할 수 있습니다.  
  
```json
"waitForFifteenMinutes" : {
    "type": "wait",
    "inputs": {
        "interval": {
            "unit" : "minute",
            "count" : 15
        }
    }
}
```  
  
또는 특정 시점까지 대기하려면 다음 예제를 사용할 수 있습니다.  
  
```json
"waitUntilOctober" : {
    "type": "wait",
    "inputs": {
        "until": {
            "timestamp" : "2016-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> 대기 시간은 **interval** 개체 또는 **until** 개체를 사용하여 지정할 수 있으나 둘 다 사용할 수는 없습니다.  
  
|이름|필수|형식|설명|  
|--------|------------|--------|---------------|  
|interval|아니요|Object|시간에 따른 대기 시간입니다.|  
|interval unit|예|String|second, minute, hour, day, week, month, year 간격 중 하나입니다.|  
|interval count|예|String|지정된 간격 단위에 따른 기간입니다.|  
|until|아니요|Object|특정 시점에 따른 대기 시간입니다.|  
|until timestamp|예|String|String&#124;대기가 만료되는 특정 시점(UTC)입니다.|  

## <a name="query-action"></a>쿼리 작업

`query` 작업을 통해 조건에 따라 배열을 필터링할 수 있습니다. 예를 들어 2보다 큰 수를 선택하려면 다음을 사용할 수 있습니다.

```json
"FilterNumbers" : {
    "type": "query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

`query` 작업의 출력은 조건을 충족하는 입력 배열의 요소를 포함하는 배열입니다.

> [!NOTE]
> `where` 조건을 충족하는 값이 없는 경우 결과는 빈 배열입니다.

|이름|필수|형식|설명|
|--------|------------|--------|---------------|
|from|예|배열|원본 배열입니다.|
|여기서,|예|String|원본 배열의 각 요소에 적용할 조건입니다.|

## <a name="select-action"></a>작업 선택

`select` 작업은 배열의 각 요소를 새 값으로 프로젝션하도록 합니다.
예를 들어 숫자 배열을 개체 배열로 변환하려면 다음을 사용할 수 있습니다.

```json
"SelectNumbers" : {
    "type": "select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

`select` 작업의 출력은 `select` 속성으로 정의되어 변환된 입력 배열과 동일한 카디널리티를 가진 배열입니다. 입력이 빈 배열인 경우 출력도 빈 배열입니다.

|이름|필수|형식|설명|
|--------|------------|--------|---------------|
|from|예|배열|원본 배열입니다.|
|선택|예|모두|원본 배열의 각 요소에 적용할 프로젝션입니다.|

## <a name="terminate-action"></a>종료 작업

종료 작업은 진행 중인 작업을 중단하고 나머지 작업을 건너뛰어 워크플로 실행을 중단합니다. 예를 들어 **실패** 상태의 실행을 종료하려면 다음 코드 조각을 사용할 수 있습니다.

```json
"HandleUnexpectedResponse" : {
    "type": "terminate",
    "inputs": {
        "runStatus" : "failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response.",
        }
    }
}
```

> [!NOTE]
> 이미 완료된 작업은 종료 작업에 의해 영향을 받지 않습니다.

|이름|필수|형식|설명|
|--------|------------|--------|---------------|
|runStatus|예|string|커넥터 실행 상태 **Failed** 또는 **Cancelled**입니다.|
|runError|아니요|Object|오류 세부 정보입니다. **runStatus**가 **Failed**로 설정된 경우에만 지원됩니다.|
|runError code|아니요|문자열|실행 오류 코드입니다.|
|runError message|아니요|문자열|실행 오류 메시지입니다.|

## <a name="compose-action"></a>작성 작업

작성 작업을 통해 임의 개체를 생성할 수 있습니다. 작성 작업의 출력은 해당 입력을 평가한 결과입니다. 예를 들어 작성 작업을 사용하여 여러 작업의 출력을 병합할 수 있습니다.

```json
"composeUserRecord" : {
    "type": "compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
        }
    }
}
```

> [!NOTE]
> **작성** 작업은 개체, 배열 및 논리 앱에서 기본적으로 지원하는 기타 형식(XML 및 이진)을 포함한 출력을 생성하는 데 사용할 수 있습니다.

## <a name="table-action"></a>테이블 작업

`table`은 항목의 배열을 **CVS** 또는 **HTML** 테이블로 변환할 수 있도록 합니다.

@triggerBody()을 다음으로 가정합니다

```json
[{
  "id": 0,
  "name": "apples"
},{
  "id": 1, 
  "name": "oranges"
}]
```

작업이 다음으로 정의되게 합니다

```json
"ConvertToTable" : {
    "type": "table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html"
    }
}
```

위의 내용은 다음을 생성합니다

<table><thead><tr><th>id</th><th>name</th></tr></thead><tbody><tr><td>0</td><td>사과</td></tr><tr><td>1</td><td>오렌지</td></tr></tbody></table>"

테이블을 사용자 지정하려면 열을 명시적으로 지정할 수 있습니다. 예:

```json
"ConvertToTable" : {
    "type": "table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [{
          "header": "produce id",
          "value": "@item().id"
        },{
          "header": "description",
          "value": "@concat('fresh ', item().name)"
        }]
    }
}
```

위의 내용은 다음을 생성합니다

<table><thead><tr><th>ID를 생성합니다</th><th>설명</th></tr></thead><tbody><tr><td>0</td><td>신선한 사과</td></tr><tr><td>1</td><td>신선한 오렌지</td></tr></tbody></table>"

`from` 속성 값이 빈 배열인 경우 출력도 빈 테이블입니다.

|이름|필수|형식|설명|
|--------|------------|--------|---------------|
|from|예|배열|원본 배열입니다.|
|format|예|string|형식은 **CVS** 또는 **HTML**입니다.|
|열|아니요|배열|열입니다. 테이블의 기본 셰이프를 재정의할 수 있습니다.|
|열 머리글|아니요|string|열의 머리글입니다.|
|열 값|예|String|열의 값입니다.|

## <a name="workflow-action"></a>워크플로 작업   

|이름|필수|형식|설명|  
|--------|------------|--------|---------------|  
|host id|예|String|호출할 워크플로의 리소스 ID입니다.|  
|host triggerName|예|String|호출할 트리거의 이름입니다.|  
|쿼리|아니요|Object|URL에 추가할 쿼리 매개 변수를 나타냅니다. 예를 들어 `"queries" : { "api-version": "2015-02-01" }`은 URL에 `?api-version=2015-02-01`을 추가합니다.|  
|headers|아니요|Object|요청에 전송된 각 헤더를 나타냅니다. 예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 합니다. `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|아니요|Object|끝점에 전송된 페이로드를 나타냅니다.|  
  
```json
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName " : "mytrigger001"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },  
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "runAfter": {}
    }
```
  
워크플로\(보다 구체적으로 트리거\)에 대해 액세스 검사가 이루어지므로 워크플로에 액세스해야 합니다.  
  
`workflow` 작업의 출력은 하위 워크플로에서 `response` 작업에 정의된 내용을 기반으로 합니다. 어떤 `response` 작업도 정의되지 않은 경우 출력은 비어 있습니다.  

## <a name="function-action"></a>Function 작업   

|이름|필수|형식|설명|  
|--------|------------|--------|---------------|  
|function id|예|string|호출하려는 함수의 리소스 ID입니다.|  
|메서드|아니요|string|함수를 호출하는 데 사용되는 HTTP 메서드입니다. 기본적으로 지정되지 않은 경우 `POST`입니다.|  
|쿼리|아니요|Object|URL에 추가할 쿼리 매개 변수를 나타냅니다. 예를 들어 `"queries" : { "api-version": "2015-02-01" }`은 URL에 `?api-version=2015-02-01`을 추가합니다.|  
|headers|아니요|Object|요청에 전송된 각 헤더를 나타냅니다. 예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 합니다. `"headers" : { "Accept-Language": "en-us" }`|  
|body|아니요|Object|끝점에 전송된 페이로드를 나타냅니다.|  

```json
"myfunc" : {
    "type" : "Function",
    "inputs" : {
        "function" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Web/sites/myfuncapp/functions/myfunc"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },  
        "headers" : {
            "x-ms-date" : "@utcnow()"
        },
        "method" : "POST",
    "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "runAfter": {}
}
```

논리 앱을 저장하는 경우 참조된 함수에 대한 몇 가지 검사를 수행합니다.
-   함수에 액세스할 수 있어야 합니다.
-   표준 HTTP 트리거 또는 일반 JSON 웹후크 트리거만 허용됩니다.
-   경로를 정의하지 않아야 합니다.
-   "함수" 및 "익명" 권한 부여 수준만 허용됩니다.

트리거 URL은 런타임에 검색, 캐시 및 사용됩니다. 따라서 어떤 작업으로 캐시된 URL이 무효화되면 런타임에 작업이 실패합니다. 이 문제를 해결하려면 논리 앱을 다시 저장합니다. 그러면 논리 앱이 트리거 URL을 다시 검색하고 캐시합니다.

## <a name="collection-actions-scopes-and-loops"></a>컬렉션 작업(범위 및 루프)

일부 작업 유형은 자체 내에 작업을 포함할 수 있습니다. 컬렉션 내에서 참조 작업은 컬렉션 외부에서 직접 참조할 수 있습니다. 범위에 `http`를 정의한 경우 `@body('http')`는 워크플로 어느 곳에서나 여전히 유효합니다. 컬렉션 내에 있는 작업은 동일한 컬렉션 내에 있는 다른 작업만 `runAfter`할 수 있습니다.

## <a name="scope-action"></a>범위 작업

`scope` 작업을 통해 워크플로에서 작업을 논리적으로 그룹화할 수 있습니다.

|이름|필수|형식|설명|  
|--------|------------|--------|---------------|  
|actions|예|Object|범위 내에서 실행할 내부 작업|

```json
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```

## <a name="foreach-action"></a>ForEach 작업

이 루프 작업은 배열을 반복하고 각 항목에 대해 내부 작업을 수행합니다. 기본적으로 foreach 루프는 병렬로 실행됩니다(한 번에 20개 실행을 병렬로 수행). `operationOptions` 매개 변수를 사용하여 실행 규칙을 설정할 수 있습니다.

|이름|필수|형식|설명|  
|--------|------------|--------|---------------|  
|actions|예|Object|루프 내에서 실행할 내부 작업|
|foreach|예|string|반복할 배열|
|operationOptions|no|string|동작에 대한 모든 작업 옵션입니다. 현재는 반복을 순차적으로 실행하는 `sequential`만 지원합니다(기본 동작은 병렬).|

```json
"forEach_email": {
    "type": "foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "runAfter":{
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>Until 작업

이 루프 작업은 조건 결과가 true일 때까지 내부 작업을 실행합니다.

|이름|필수|형식|설명|  
|--------|------------|--------|---------------|  
|actions|예|Object|루프 내에서 실행할 내부 작업|
|식|예|string|각 반복 후마다 평가할 식|
|limit|yes|Object|루프에 대한 제한 - 제한을 하나 이상 정의해야 함|
|count|no|int|수행할 수 있는 반복 수에 대한 제한|
|시간 제한|no|string|반복 기간에 대한 시간 제한입니다.  ISO 8601 형식|


```json
 "Until_succeeded": {
    "actions": {
        "Http": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {},
    "type": "Until"
}
```

## <a name="conditions---if-action"></a>조건 - If 작업

`If` 작업을 통해 조건을 평가하고 식이 `true`로 평가되는지 여부에 따라 분기를 실행할 수 있습니다.

|이름|필수|형식|설명|  
|--------|------------|--------|---------------|  
|actions|예|Object|식이 `true`로 평가될 때 실행할 내부 작업|
|식|예|string|평가할 식|
|else|no|Object|식이 `false`로 평가될 때 실행할 내부 작업|
  
```json
"My_condition": {
    "actions": {
        "If_true": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "else": {
        "actions": {
            "if_false": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://myurl"
                },
                "runAfter": {},
                "type": "Http"
            }
        }
    },
    "expression": "@equals(triggerBody(), json(true))",
    "runAfter": {},
    "type": "If"
}
```  
  
다음 표에서는 조건에서 작업에 식을 사용하는 방법에 대한 예를 보여 줍니다.  
  
|JSON 값|결과|  
|--------------|----------|  
|`"expression": "@parameters('hasSpecialAction')"`|true로 평가되는 모든 값을 통해 이 조건을 통과합니다. 부울 식만 지원됩니다. 다른 형식을 부울로 변환하려면 `empty`, `equals` 함수를 사용합니다.|  
|`"expression": "@greater(actions('act1').output.value, parameters('threshold'))"`|비교 함수는 지원됩니다. 여기에 표시된 예제에서는 act1의 출력이 임계값보다 큰 경우에만 작업이 실행됩니다.|  
|`"expression": "@or(greater(actions('act1').output.value, parameters('threshold')), less(actions('act1').output.value, 100))"`|중첩된 부울 식을 만들기 위해 논리 함수도 지원됩니다. 이 경우 act1의 출력이 임계값보다 크거나 100 미만인 경우 작업이 실행됩니다.|  
|`"expression": "@equals(length(actions('act1').outputs.errors), 0))"`|배열 함수를 사용하여 배열에 항목이 있는지 확인할 수 있습니다. 이 경우 오류 배열이 비어 있으면 작업이 실행됩니다.| 
|`"expression": "parameters('hasSpecialAction')"`|오류 - 조건에 @이 필요하므로 유효한 조건이 아닙니다.|  
  
조건이 성공적으로 평가되면 조건이 `Succeeded`로 표시됩니다. `actions` 또는 `else` 개체 내의 작업이 실행되어 성공하면 `Succeeded`로 평가되고, 실행되어 실패하면 `Failed`로, 분기가 실행되지 않으면 `Skipped`로 평가됩니다.

## <a name="next-steps"></a>다음 단계

[워크플로 서비스 REST API](https://docs.microsoft.com/rest/api/logic/workflows)

