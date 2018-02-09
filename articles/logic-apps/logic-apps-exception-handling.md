---
title: "Azure에서 Logic Apps에 대한 오류 및 예외 처리 | Microsoft Docs"
description: "Logic Apps의 오류 및 예외 처리 패턴"
services: logic-apps
documentationcenter: .net,nodejs,java
author: derek1ee
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: a74c7d18306359c9152f139299de1208b5932fe5
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/20/2017
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Logic Apps에서 오류 및 예외 처리

Azure의 Logic Apps는 통합이 견고하고 오류로부터 복구되도록 지원하는 풍부한 도구 및 패턴을 제공합니다. 모든 통합 아키텍처에는 종속 시스템의 문제나 가동 중지 시간을 적절하게 처리해야 하는 과제가 있습니다. Logic Apps는 오류 처리를 최고 수준 환경으로 만듭니다. 또한 워크플로에서 예외 및 오류에 대해 작업을 수행하는 데 필요한 도구를 제공합니다.

## <a name="retry-policies"></a>재시도 정책

재시도 정책은 가장 기본적인 예외 및 오류 처리 형식입니다. 초기 요청이 시간 초과되거나 실패한 경우(429 또는 5xx 응답이 발생하는 모든 요청) 재시도 정책은 작업을 재시도해야 하는지 여부 및 재시도 방법을 정의합니다. 

기본값, 없음, 고정 간격 및 기하급수적 간격의 네 가지 재시도 정책 유형이 있습니다. 재시도 정책이 워크플로 정의에 제공되지 않는 경우에는 서비스에서 정의된 기본 정책이 사용됩니다. 

재시도가 가능한 경우 특정 작업 또는 트리거의 *입력*에서 재시도 정책을 구성할 수 있습니다. 마찬가지로 Logic Apps 디자이너에서 재시도 정책(해당되는 경우)을 구성할 수 있습니다. 재시도 정책을 설정하려면 Logic Apps 디자이너에서 특정 작업에 대한 **설정**으로 이동합니다.

재시도 정책의 제한 사항에 대한 자세한 내용은 [Logic Apps 제한 및 구성](../logic-apps/logic-apps-limits-and-config.md)을 참조하세요. 지원되는 구문에 대한 자세한 내용은 [워크플로 작업 및 트리거에서 재시도 정책 섹션][retryPolicyMSDN]을 참조하세요.

### <a name="default"></a>기본값

재시도 정책을 정의하지 않으면(**retryPolicy**가 정의되지 않음) 기본 정책이 사용됩니다. 기본 정책은 간격을 7.5초마다 조정되는 기하급수적으로 증가하는 간격으로 최대 4번의 재시도를 전송하는 기하급수적 간격 정책입니다. 간격은 5~45초 사이에서 제한됩니다. 이 기본 정책은 이 예제 HTTP 워크플로 정의의 정책에 해당합니다.

```json
"HTTP":
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7.5S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT45S"
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

### <a name="none"></a>없음

**retryPolicy**가 **없음**으로 설정된 경우 실패한 요청이 다시 시도되지 않습니다.

| 요소 이름 | 필수 | type | 설명 |
| ------------ | -------- | ---- | ----------- |
| 형식 | 예 | 문자열 | **없음** |

### <a name="fixed-interval"></a>고정 간격

**retryPolicy**가 **고정**으로 설정된 경우 정책은 다음 요청을 전송하기 전에 지정된 시간 간격 동안 대기하여 실패한 요청을 다시 시도합니다.

| 요소 이름 | 필수 | type | 설명 |
| ------------ | -------- | ---- | ----------- |
| 형식 | 예 | 문자열 | **고정** |
| count | 예 | 정수  | 재시도 횟수입니다. 1~90 사이여야 합니다. |
| interval | 예 | 문자열 | [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)의 재시도 간격. PT5S~PT1D 사이여야 합니다. |

### <a name="exponential-interval"></a>기하급수적 간격

**retryPolicy**가 **기하급수적**으로 설정된 경우 정책은 기하급수적으로 증가하는 범위에서 무작위 시간 간격 후 실패한 요청을 다시 시도합니다. 각 다시 시도는 **minimumInterval**보다 크고 **maximumInterval**보다 작은 임의 간격으로 전송되도록 보장됩니다. 다음 표에 표시된 범위의 균일한 임의 변수는 각 재시도에 대해 **count** 이하로 생성됩니다.

**임의 변수 범위**

| 재시도 횟수 | 최소 간격 | 최대 간격 |
| ------------ |  ------------ |  ------------ |
| 1 | Max(0, **minimumInterval**) | Min(interval, **maximumInterval**) |
| 2 | Max(interval, **minimumInterval**) | Min(2 * interval, **maximumInterval**) |
| 3 | Max(2 * interval, **minimumInterval**) | Min(4 * interval, **maximumInterval**) |
| 4 | Max(4 * interval, **minimumInterval**) | Min(8 * interval, **maximumInterval**) |
| ... |

기하급수적 유형 정책의 경우 **count** 및 **interval**이 필요합니다. **minimumInterval** 및 **maximumInterval** 값은 선택 사항입니다. 값을 추가하여 각각 PT5S 및 PT1D의 기본값을 재정의할 수 있습니다.

| 요소 이름 | 필수 | type | 설명 |
| ------------ | -------- | ---- | ----------- |
| 형식 | 예 | 문자열 | **기하급수적** |
| count | 예 | 정수  | 재시도 횟수입니다. 1~90 사이여야 합니다.  |
| interval | 예 | 문자열 | [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)의 재시도 간격. PT5S~PT1D 사이여야 합니다. |
| minimumInterval | 아니오 | 문자열 | [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)의 재시도 최소 간격. PT5S와 **간격** 사이여야 합니다. |
| maximumInterval | 아니요 | 문자열 | [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)의 재시도 최소 간격. **간격**과 PT1D 사이여야 합니다. |

## <a name="catch-failures-with-the-runafter-property"></a>runAfter 속성을 사용하여 오류 Catch

각 논리 앱 작업은 작업이 시작되기 전에 마쳐야 할 작업을 선언합니다. 워크플로의 단계 순서 지정과 비슷합니다. 이 순서를 작업 정의에서 **runAfter** 속성이라고 합니다. 

**runAfter** 속성은 작업을 실행할 작업 및 작업 상태를 설명하는 개체입니다. 기본적으로 Logic Apps 디자이너를 사용하여 추가한 모든 작업은 이전 단계 결과가 **성공**인 경우 이전 단계 이후에 실행되도록 설정됩니다. 

그러나 이전 작업의 결과가 **실패**, **건너뜀** 또는 이러한 값의 가능한 조합인 경우 작업을 실행하도록 **runAfter** 값을 사용자 지정할 수 있습니다. 특정 작업 **Insert_Row**가 실패한 후, 지정된 Azure Service Bus 항목에 항목을 추가하려면 다음 **runAfter** 구성을 사용할 수 있습니다.

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

**Insert_Row** 작업 결과가 **실패**인 경우 **runAfter**가 실행되도록 설정됩니다. 작업 상태가 **성공**, **실패** 또는 **건너뜀**인 경우 작업을 실행하려면 다음 구문을 사용합니다.

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> 이전 작업이 실패한 후 실행되고 성공적으로 완료되는 작업은 **성공**으로 표시됩니다. 이는 워크플로에서 모든 오류를 성공적으로 catch한 경우 실행 자체가 **성공**으로 표시됨을 의미합니다.

## <a name="scopes-and-results-to-evaluate-actions"></a>작업을 평가할 범위 및 결과

개별 작업 후에 실행되는 방법과 비슷하게 [범위](../logic-apps/logic-apps-loops-and-scopes.md) 내에서 작업을 그룹화할 수 있습니다. 범위는 작업의 논리 그룹으로 사용됩니다. 

범위는 Logic Apps 작업을 구성하고 범위 상태에 대한 집계 평가를 수행하는 데 유용합니다. 범위 자체는 범위의 작업을 모두 완료한 후에 상태를 수신합니다. 범위 상태는 실행되는 동일한 조건을 사용하여 결정됩니다. 실행 분기의 최종 작업이 **실패** 또는 **중단됨**인 경우 상태는 **실패**입니다.

범위 내에서 발생한 모든 오류에 대해 특정 작업을 실행하려면 **실패**로 표시된 범위에서 **runAfter**를 사용할 수 있습니다. 범위 내의 *모든* 작업이 실패한 경우 범위에 **runAfter**를 사용하면 오류를 catch하는 단일 작업을 만들 수 있습니다.

### <a name="get-the-context-of-failures-with-results"></a>결과와 함께 오류 컨텍스트 가져오기

범위에서 오류를 catch하는 것은 매우 유용하지만 정확히 어떤 작업이 실패했는지 파악하도록 돕고 어떤 오류 또는 상태 코드가 반환되었는지 파악하기 위해 컨텍스트가 필요할 수 있습니다. **@result()** 워크플로 함수는 범위에 있는 모든 작업의 결과에 대한 컨텍스트를 제공합니다.

**@result()** 함수에서는 단일 매개 변수, 범위 이름을 사용하고 해당 범위 내에서 발생하는 모든 작업의 배열을 반환합니다. 이러한 작업 개체는 작업 시작 시간, 작업 종료 시간, 작업 상태, 작업 입력, 작업 상관 관계 ID 및 작업 출력 등 **@actions()** 개체와 동일한 특성을 포함합니다. 

범위 내에서 실패한 작업의 컨텍스트를 보내려면 **@result()** 함수를 **runAfter** 속성과 쉽게 페어링할 수 있습니다.

**실패** 결과가 포함된 범위에 있는 *각* 작업에 대해 작업을 실행하고 실패한 작업에 대한 결과 배열을 필터링하려면 [Filter_array](../connectors/connectors-native-query.md) 작업 및 [foreach](../logic-apps/logic-apps-loops-and-scopes.md) 루프와 **@result()**를 페어링할 수 있습니다. 필터링된 결과 배열을 사용하고 **foreach** 루프를 사용하여 각 오류에 대한 작업을 수행할 수 있습니다. 

다음 예제에서는 My_Scope 범위에서 실패한 작업의 응답 본문과 함께 HTTP POST 요청을 보냅니다.

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

이전 예제에서 발생한 상황을 설명하는 자세한 연습은 다음과 같습니다.

1. My_Scope 내에서 모든 작업의 결과를 가져오기 위해 **Filter_array** 작업은 **@result('My_Scope')**를 필터링합니다.

2. **Filter_array**에 대한 조건은 상태가 **실패**인 모든 **@result()** 항목입니다. 이 조건은 My_Scope의 모든 작업 결과에 대한 배열을 실패한 작업 결과만 포함된 배열로 필터링합니다.

3. *필터링된 배열* 출력에서 **foreach** 작업을 수행합니다. 이 단계는 이전에 필터링된 실패한 작업 결과 *각각에 대해* 작업을 수행합니다.

    범위에서 단일 작업이 실패한 경우 **foreach**의 작업은 한 번만 실행됩니다. 여러 실패한 작업에서 오류당 하나의 작업이 발생합니다.

4. **foreach** 항목 응답 본문 또는 **@item()['outputs']['body']**에서 HTTP POST를 보냅니다. **@result()** 항목 모양은 **@actions()** 모양과 같습니다. 동일한 방법으로 구문 분석할 수 있습니다.

5. 실패한 작업 이름 **@item()[’name’]** 및 실패한 실행 클라이언트 추적 ID **@item()['clientTrackingId']**가 있는 두 개의 사용자 지정 헤더도 포함됩니다.

참조를 위해 다음은 단일 **@result()** 항목의 예입니다. 이전 예제에서 구문 분석되는 **name**, **body** 및 **clientTrackingId** 속성을 보여 줍니다. **foreach** 작업 외부에서 **@result()**는 이러한 개체의 배열을 반환합니다.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/folder-name/resource-name does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

다른 예외 처리 패턴의 경우 문서 앞부분에서 설명한 식을 사용할 수 있습니다. 오류의 전체 필터링된 배열을 허용하는 범위 외부에서 단일 예외 처리 작업을 실행하고 **foreach**를 제거하도록 선택할 수 있습니다. 또한 이전에 설명한 대로 **@result()** 응답에서 기타 유용한 속성을 포함할 수도 있습니다.

## <a name="azure-diagnostics-and-telemetry"></a>Azure 진단 및 원격 분석

이 문서에서 설명한 패턴은 실행 내에서 오류 및 예외를 처리하는 훌륭한 방법이지만 실행 자체와는 독립적으로 오류를 식별하고 오류에 대응할 수도 있습니다. [Azure 진단](../logic-apps/logic-apps-monitor-your-logic-apps.md)은 모든 워크플로 이벤트(모든 실행 및 작업 상태 포함)를 Azure Storage 계정 또는 Azure Event Hubs의 이벤트 허브로 보내는 간단한 방법을 제공합니다. 

실행 상태를 평가하려면 로그 및 메트릭을 모니터링하거나 선호하는 모니터링 도구로 게시할 수 있습니다. 한 가지 잠재적인 옵션은 Event Hubs를 통해 모든 이벤트를 [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)로 스트리밍하는 것입니다. Stream Analytics에서는 진단 로그의 모든 잘못된 부분, 평균 또는 오류를 기반으로 라이브 쿼리를 작성할 수 있습니다. Stream Analytics를 사용하여 큐, 토픽, SQL, Azure Cosmos DB 및 Power BI와 같은 기타 데이터 원본에 정보를 보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 고객이 [Azure Logic Apps의 오류 처리를 빌드](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)하는 방법을 참조하세요.
* [Logic Apps 예제 및 시나리오](../logic-apps/logic-apps-examples-and-scenarios.md)을 더 찾아보세요.
* [논리 앱에 자동화된 배포](../logic-apps/logic-apps-create-deploy-template.md)를 만드는 방법을 알아보세요.
* [Visual Studio에서 논리 앱을 빌드 및 배포](logic-apps-deploy-from-vs.md)하는 방법을 알아보세요.

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
