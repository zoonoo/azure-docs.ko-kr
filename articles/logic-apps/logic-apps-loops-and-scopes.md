---
title: "워크플로에서 루프와 범위 만들기 및 데이터 분리 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps에서 루프를 만들어 데이터를 반복하거나, 작업을 범위로 그룹화하거나, 데이터를 분리하여 더 많은 워크플로를 시작합니다."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: a17de187f67c075147ea8ff7f69434014eea3fdb
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2017
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Logic Apps 루프, 범위 및 분할
  
Logic Apps는 워크플로 내에서 배열, 컬렉션, 일괄 처리 작업 및 루프를 다양한 방법으로 제공합니다.
  
## <a name="foreach-loop-and-arrays"></a>ForEach 루프 및 배열
  
Logic Apps를 사용하면 데이터 집합을 반복하고 각 항목에 대해 작업을 수행할 수 있습니다.  이는 `foreach` 작업을 통해 가능합니다.  디자이너에서 ForEach 루프를 추가하도록 지정할 수 있습니다.  반복하려는 배열을 선택한 후 작업 추가를 시작할 수 있습니다.  ForEach 루프당 여러 작업을 추가할 수 있습니다.  일단 루프 내에 들어오면 배열의 각 값에 나오는 항목을 지정할 수 있습니다.

코드 보기를 사용하는 경우 아래와 같이 각 루프에 지정할 수 있습니다.  다음은 'microsoft.com'을 포함하는 각 전자 메일 주소에 대해 전자 메일을 보내는 각 루프의 예입니다.

``` json
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')"
        }
    },
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
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                },
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  `foreach` 작업은 5,000개 행까지 배열을 반복할 수 있습니다.  각 반복은 기본적으로 병렬로 실행 됩니다.  

### <a name="sequential-foreach-loops"></a>순차적 ForEach 루프

순차적으로 실행하는 foreach 루프를 사용 하도록 설정하려면 `Sequential` 작업 옵션을 추가해야 합니다.

``` json
"forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "operationOptions": "Sequential",
        "..."
}
```
  
## <a name="until-loop"></a>Until 루프
  
  조건이 충족될 때까지 작업 또는 일련의 작업을 수행할 수 있습니다.  이에 대한 가장 일반적인 시나리오는 원하는 응답을 얻을 때까지 끝점을 호출하는 것입니다.  디자이너에서 Until 루프를 추가하도록 지정할 수 있습니다.  이 루프 내에 작업을 추가한 후에 루프 한도 뿐만 아니라 종료 조건을 설정할 수 있습니다.  루프 주기 사이에는 1분의 지연이 발생합니다.
  
  코드 보기를 사용하는 경우 아래와 같이 Until 루프를 지정할 수 있습니다.  다음은 응답 본문 값이 'Completed'가 될 때까지 HTTP 끝점을 호출하는 예제입니다.  다음과 같은 경우에 완료됩니다. 
  
  * HTTP 응답의 상태가 'Completed'임
  * 1시간 동안 시도됨
  * 100번 반복됨
  
  ``` json
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed')",
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn 및 분리

경우에 따라 트리거가 항목당 워크플로를 분리하고 시작하려는 항목 배열을 수신할 수 있습니다.  이 작업은 `spliton` 명령을 통해 수행될 수 있습니다.  기본적으로 트리거 swagger가 배열에 해당하는 페이로드를 지정하는 경우 `spliton`이 추가되고 항목당 실행이 시작됩니다.  SplitOn만 트리거로 추가할 수 있습니다.  정의 코드 보기에서 수동으로 구성하거나 재정의할 수 있습니다.  현재 SplitOn은 배열을 최대 5,000개의 항목으로 분리할 수 있습니다.  `spliton`을 포함할 수 없으며 동기 응답 패턴도 구현할 수 없습니다.  `spliton` 외에도 `response` 작업이 있는 호출된 모든 워크플로는 비동기식으로 실행되고 즉각적인 `202 Accepted` 응답을 전송합니다.  

다음 예제와 같이 코드 보기에서 SplitOn을 지정할 수 있습니다.  이렇게 하면 항목의 배열이 수신되고 각 행에서 분리됩니다.

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>범위

범위를 사용하여 일련의 작업을 함께 그룹화할 수 있습니다.  이 기능은 예외 처리를 구현하는 데 특히 유용합니다.  디자이너에서 새 범위를 추가하고 내부에 작업을 추가할 수 있습니다.  다음과 같이 코드 보기에서 범위를 정의할 수 있습니다.


```
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
