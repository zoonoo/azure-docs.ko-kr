---
title: "JSON으로 워크플로 정의 - Azure Logic Apps | Microsoft Docs"
description: "JSON에서 논리 앱용 워크플로 정의를 작성하는 방법"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/29/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 7f9e5a10066df8a464c285273e77a85c0d562ebb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="create-workflow-definitions-for-logic-apps-using-json"></a>JSON을 사용하여 논리 앱용 워크플로 정의 만들기

간단하고 선언적인 JSON 언어로 [Azure Logic Apps](logic-apps-what-are-logic-apps.md)에 대한 워크플로 정의를 만들 수 있습니다. 아직 없는 경우 [Logic App Designer에서 첫 번째 논리 앱을 만드는 방법](logic-apps-create-a-logic-app.md)을 먼저 검토하세요. 또한 [워크플로 정의 언어에 대한 전체 참조](http://aka.ms/logicappsdocs)도 참조하세요.

## <a name="repeat-steps-over-a-list"></a>목록에 대한 단계 반복

최대 10,000개의 항목이 포함된 배열을 반복하고 각 항목에 대해 작업을 수행하고 [foreach 형식](logic-apps-loops-and-scopes.md)을 사용합니다.

## <a name="handle-failures-if-something-goes-wrong"></a>문제가 발생한 경우의 오류 처리

일반적으로 하나 이상의 호출이 실패한 *경우에 한해* 일부 논리를 실행하는 *재구성 단계*를 포함하기를 원합니다. 이 예제에서는 다양한 위치에서 데이터를 가져오지만 호출이 실패한 경우에는 나중에 오류를 추적할 수 있는 곳에 메시지를 게시하고자 합니다.  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "postToErrorMessageQueue": {
      "type": "ApiConnection",
      "inputs": "...",
      "runAfter": {
        "readData": [
          "Failed"
        ]
      }
    }
  },
  "outputs": {}
}
```

`readData`가 `Failed`한 후에만 `postToErrorMessageQueue`가 실행되도록 지정하려면 `runAfter` 속성을 사용합니다. 예를 들어 가능한 값 목록을 지정하려면 `runAfter`는 `["Succeeded", "Failed"]`일 수 있습니다.

마지막으로, 이제 오류를 처리했으므로 더 이상 실행을 `Failed`로 표시하지 않습니다. 이 예제에서는 오류 처리를 위한 단계를 추가했으므로 한 단계가 `Failed`하더라도 실행은 `Succeeded`합니다.

## <a name="execute-two-or-more-steps-in-parallel"></a>둘 이상의 단계를 병렬로 실행

여러 작업을 병렬로 실행하려면 `runAfter` 속성은 런타임 시 동일해야 합니다. 

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "kind": "http",
      "type": "Request"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

이 예제에서는 `branch1` 및 `branch2` 모두 `readData` 후에 실행되도록 설정됩니다. 따라서 두 분기가 병렬로 실행됩니다. 두 분기에 대한 타임스탬프는 동일합니다.

![병렬](media/logic-apps-author-definitions/parallel.png)

## <a name="join-two-parallel-branches"></a>두 병렬 분기의 조인

이전 예제에서처럼 `runAfter` 속성에 항목을 추가하여 병렬로 실행하도록 설정된 두 가지 작업을 조인할 수 있습니다.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {}
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "join": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "branch1": [
          "Succeeded"
        ],
        "branch2": [
          "Succeeded"
        ]
      }
    }
  },
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "Http",
      "inputs": {
        "schema": {}
      }
    }
  },
  "contentVersion": "1.0.0.0",
  "outputs": {}
}
```

![병렬](media/logic-apps-author-definitions/join.png)

## <a name="map-list-items-to-a-different-configuration"></a>목록 항목을 다른 구성에 매핑

다음으로 속성의 값을 기반으로 다른 콘텐츠를 가져오는 경우를 다루겠습니다. 매개 변수를 대상으로 하는 값의 맵을 만들 수 있습니다.  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "http://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

이 경우 먼저 문서 목록을 가져옵니다. 매개 변수로 정의된 범주에 따라, 두 번째 단계에서는 콘텐츠를 가져오기 위한 URL을 조회하는 맵을 사용합니다.

경우에 따라 다음을 유의합니다. 

*   [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) 함수는 해당 범주가 알려진 정의된 범주 중 하나와 일치하는지 여부를 확인합니다.

*   범주를 가져온 후에는 대괄호를 사용하여 맵에서 항목을 끌어올 수 있습니다. `parameters[...]`

## <a name="process-strings"></a>문자열 처리

문자열을 조작하기 위해 다양한 함수를 사용할 수 있습니다. 예를 들어 시스템으로 전달하고자 하는 문자열이 있지만 문자 인코딩을 위한 적절한 처리에 자신이 없다고 가정해 보겠습니다. 하나의 옵션은 이 문자열을 base64 인코딩하는 것입니다. 그러나 URL에서 이스케이프를 방지하기 위해 몇 문자를 대체할 것입니다. 

또한 첫 5자는 사용되지 않으므로 명령 이름의 하위 문자열을 원합니다.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "orderer": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

내부에서 외부로 작업:

1. 명령자 이름의 [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length)를 가져오므로 전체 문자 수를 다시 가져옵니다.

2. 더 짧은 문자열을 원하므로 5를 뺍니다.

3. 실제로 [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring)을(를) 가져옵니다. 인덱스 `5` 에서 시작하여 문자열의 나머지로 이동합니다.

4. 이 하위 문자열을 [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) 문자열로 변환합니다.

5. 모든 `+` 문자를 `-` 문자로 [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)합니다.

6. 모든 `/` 문자를 `_` 문자로 [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)합니다.

## <a name="work-with-date-times"></a>날짜 시간을 사용한 작업

날짜 시간은 자연스럽게 *트리거*를 지원하지 않는 데이터 원본에서 데이터를 가져오려고 할 때 특히 유용할 수 있습니다. 날짜 시간은 다양한 단계에 소요되는 시간을 파악하는 데에도 사용할 수 있습니다.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

이 예제에서는 이전 단계의 `startTime`을(를) 추출합니다. 그런 다음 현재 시간 가져오고 1초를 뺍니다.

[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) 

`minutes` 또는 `hours`와 같은 다른 시간 단위를 사용할 수 있습니다. 마지막으로 이 두 값을 비교할 수 있습니다. 첫 번째 값이 두 번째 값보다 작은 경우에는 명령이 처음으로 배치된 후 1초 이상의 시간이 경과된 것입니다.

날짜에 서식을 지정하려면 문자열 포맷터를 사용할 수 있습니다. 예를 들어 RFC1123을 얻으려면 [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow)를 사용합니다. 날짜 서식 지정에 대해 알아보려면 [워크플로 정의 언어](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow)를 참조하세요.

## <a name="deployment-parameters-for-different-environments"></a>다른 환경에 대한 배포 매개 변수

일반적으로 배포 주기는 배포 환경, 스테이징 환경 및 프로덕션 환경으로 구성됩니다. 예를 들어, 이러한 모든 환경에서 동일한 정의를 사용하지만 다른 데이터베이스를 사용할 수 있습니다. 마찬가지로, 높은 가용성을 위해 다양한 하위 지역에 걸쳐 동일한 정의를 사용하고자 하지만 해당 하위 지역의 데이터베이스와 통신하는 각각의 논리 앱 인스턴스를 원할 수 있습니다.
이 시나리오는 대신 *런타임*에 매개 변수를 사용하는 것과 다르며 이전 예제와 같이 `trigger()` 함수를 사용해야 합니다.

다음 예제와 같은 기본적인 정의로 시작할 수 있습니다.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

논리 앱에 대한 실제 `PUT` 요청에서 매개 변수 `uri`를 제공할 수 있습니다. 기본값이 더 이상 존재하지 않으므로 논리 앱 페이로드에 이 매개 변수가 필요합니다.

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

각 환경에서 `connection` 매개 변수에 대해 다른 값을 제공할 수 있습니다. 

논리 앱 만들기 및 관리에 대해 포함할 모든 옵션은 [REST API 설명서](https://msdn.microsoft.com/library/azure/mt643787.aspx)를 참조하세요. 
