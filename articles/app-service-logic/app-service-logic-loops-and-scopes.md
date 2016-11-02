<properties
   pageTitle="논리 앱 루프, 범위 및 분리 | Microsoft Azure"
   description="논리 앱 루프, 범위 및 분리 개념"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# 논리 앱 루프, 범위 및 분할
  
>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2016-04-01-preview 이상 스키마 버전에 적용됩니다. 개념은 이전 스키마와 유사하지만 범위는 이 스키마 이상에서만 사용할 수 있습니다.
  
## ForEach 루프 및 배열
  
논리 앱을 사용하면 데이터 집합을 반복하고 각 항목에 대해 작업을 수행할 수 있습니다. 이는 `foreach` 작업을 통해 가능합니다. 디자이너에서 ForEach 루프를 추가하도록 지정할 수 있습니다. 반복하려는 배열을 선택한 후 작업 추가를 시작할 수 있습니다. 현재는 ForEach 루프당 하나의 작업으로만 제한되지만 이러한 제한이 향후 몇 주 후에는 변경될 예정입니다. 일단 루프 내에 들어오면 배열의 각 값에 나오는 항목을 지정할 수 있습니다.

코드 보기를 사용하는 경우 아래와 같은 ForEach 루프를 지정할 수 있습니다. 다음은 'microsoft.com'을 포함하는 각 전자 메일 주소에 대해 전자 메일을 보내는 ForEach 루프의 예입니다.

```
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')
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
}
```
  
  `foreach` 작업은 5,000개 행까지 배열을 반복할 수 있습니다. 각 반복은 동시에 실행될 수 있으므로 흐름 제어가 필요한 경우 큐에 메시지를 추가해야 할 수 있습니다.
  
## Until 루프
  
  조건이 충족될 때까지 작업 또는 일련의 작업을 수행할 수 있습니다. 이에 대한 가장 일반적인 시나리오는 원하는 응답을 얻을 때까지 끝점을 호출하는 것입니다. 디자이너에서 Until 루프를 추가하도록 지정할 수 있습니다. 이 루프 내에 작업을 추가한 후에 루프 한도 뿐만 아니라 종료 조건을 설정할 수 있습니다. 루프 주기 사이에는 1분의 지연이 발생합니다.
  
  코드 보기를 사용하는 경우 아래와 같이 Until 루프를 지정할 수 있습니다. 다음은 응답 본문 값이 'Completed'가 될 때까지 HTTP 끝점을 호출하는 예제입니다. 다음과 같은 경우에 완료됩니다.
  
  * HTTP 응답의 상태가 'Completed'임
  * 1시간 동안 시도됨
  * 100번 반복됨
  
  ```
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed'),
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
  
## SplitOn 및 분리

경우에 따라 트리거가 항목당 워크플로를 분리하고 시작하려는 항목 배열을 수신할 수 있습니다. 이 작업은 `spliton` 명령을 통해 수행될 수 있습니다. 기본적으로 트리거 swagger가 배열에 해당하는 페이로드를 지정하는 경우 `spliton`이 추가되고 항목당 실행이 시작됩니다. SplitOn만 트리거로 추가할 수 있습니다. 정의 코드 보기에서 수동으로 구성하거나 재정의할 수 있습니다. 현재 SplitOn은 배열을 최대 5,000개의 항목으로 분리할 수 있습니다. `spliton`을 포함할 수 없으며 동기 응답 패턴도 구현할 수 없습니다. `spliton` 외에 `response` 작업이 있는 호출된 모든 워크플로는 비동기식으로 실행되고 즉각적인 `202 Accepted` 응답을 전송합니다.

다음 예제와 같이 코드 보기에서 SplitOn을 지정할 수 있습니다. 이렇게 하면 항목의 배열이 수신되고 각 행에서 분리됩니다.

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

## 범위

범위를 사용하여 일련의 작업을 함께 그룹화할 수 있습니다. 이 기능은 예외 처리를 구현하는 데 특히 유용합니다. 디자이너에서 새 범위를 추가하고 내부에 작업을 추가할 수 있습니다. 다음과 같이 코드 보기에서 범위를 정의할 수 있습니다.


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

<!---HONumber=AcomDC_0803_2016-->