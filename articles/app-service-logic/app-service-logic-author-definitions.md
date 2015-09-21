<properties 
	pageTitle="작성자 논리 앱 정의" 
	description="논리 앱에 대한 JSON 정의 작성 방법을 알아봅니다." 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2015"
	ms.author="stepsic"/>
	
#작성자 논리 앱 정의
이 항목에서는 간단하고 선언적인 JSON 언어인 [앱 서비스 논리 앱](app-service-logic-what-are-logic-apps.md) 정의를 사용하는 방법을 설명합니다. 아직 [새 논리 앱 만드는 방법](../app-service-create-a-logic-app.md)을 확인하지 않았다면 먼저 확인하십시오. [MSDN 정의 언어의 전체 참조 자료](https://msdn.microsoft.com/library/azure/dn948512.aspx)를 읽을 수도 있습니다.

## 목록에서 반복되는 여러 단계

일반적인 패턴은 항목 목록을 가져오는 하나의 단계 이후에, 목록에서 하고자 하는 둘 이상의 일련의 작업이 옵니다.

![목록 반복](./media/app-service-logic-author-definitions/repeatoverlists.png)

이 예제에서는 3개의 작업이 있습니다.

1. 문서 목록을 가져옵니다. 이는 배열을 포함하는 개체를 다시 반환합니다.

2. 각 문서에서 링크 속성으로 이동하는 작업은 문서의 실제 위치를 다시 반환하게 됩니다.

3. 두 번째 작업에서 실제 문서 다운로드에 이르는 모든 결과를 반복하는 작업입니다.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            }
        },
        "readLinks": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem().link"
            },
            "repeat": "@body('getArticles').responseData.feed.entries"
        },
        "downloadLinks": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatOutputs().headers.location"
            },
            "conditions": [
                {
                    "expression": "@not(equals(actions('readLinks').status, 'Skipped'))"
                }
            ],
            "repeat": "@actions('readLinks').outputs.repeatItems"
        }
    },
    "outputs": {}
}
```

[논리 앱 기능 사용](app-service-logic-use-logic-app-features.md)에서 살펴본 바와 같이, 두 번째 작업의 `repeat:` 속성을 사용하여 첫 번째 목록을 반복합니다. 그러나 세 번째 작업의 경우에는 각 문서에 대해 두 번째 작업이 실행되므로 `@actions('readLinks').outputs.repeatItems` 속성을 선택해야 합니다.

작업 내부에서 [`repeatItem()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatItem), [`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs) 또는 [`repeatBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatBody) 함수를 사용할 수 있습니다. 이 예제에서는 `location` 헤더를 가져오고자 하므로 [`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs) 함수를 사용해서 현재 반복 중인 두 번째 작업의 작업 실행 출력을 가져왔습니다.

## 일부 다른 구성에는 목록에서 항목 매핑

다음으로 속성의 값에 따라 완전히 다른 콘텐츠를 가져오는 경우를 다루겠습니다. 매개 변수를 대상으로 하는 값의 맵을 만들 수 있습니다.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
                "microsoft": "https://www.microsoft.com/ko-KR/default.aspx",
                "google": "https://www.google.com",
                "robots": "https://en.wikipedia.org/wiki/Robot",
                "NSA": "https://www.nsa.gov/"
            },
            "type": "Object"
        }
    },
    "triggers": {},
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            },
            "conditions": []
        },
        "getSpecialPage": {
            "repeat": "@body('getArticles').responseData.feed.entries",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(repeatItem().categories, parameters('specialCategories')))]"
            },
            "conditions": [
                {
                    "expression": "@greater(length(intersection(repeatItem().categories, parameters('specialCategories'))), 0)"
                }
            ]
        }
    }
}
```

이 경우, 먼저 문서 목록을 가져온 후 매개 변수로 정의된 카테고리에 기반하여 두 번째 단계가 맵에서 콘텐츠를 가져 올 URL을 조회합니다.

여기서는 다음 두 항목에 주의해야 합니다. [`intersection()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#intersection) 함수는 카테고리가 정의된 알려진 카테고리 중 하나에 일치하는지 확인하는 데 사용됩니다. 둘째로 일단 카테고리를 가져오면 대괄호를 사용하여 맵의 항목을 끌어올 수 있습니다: `parameters[...]`

## 리스트 반복 중의 체인/중첩 논리 앱

더 신중한 경우, 논리 앱을 관리하는 것이 더 쉬울 수 있습니다. 논리를 복수의 정의로 인수 분해하고 동일한 부모 정의에서 이를 호출하여 수행할 수 있습니다. 이 예제에서는 명령을 받는 부모 논리 앱과 각 명령에 대해 일부 단계를 실행하는 자식 논리 앱이 있게 됩니다.

부모 논리 앱에서는:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "orders": {
            "defaultValue": [
                {
                    "quantity": 10,
                    "id": "myorder1"
                },
                {
                    "quantity": 200,
                    "id": "specialOrder"
                },
                {
                    "quantity": 5,
                    "id": "myOtherOrder"
                }
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "iterateOverOrders": {
            "repeat": "@parameters('orders')",
            "type": "Workflow",
            "inputs": {
                "uri": "https://westus.logic.azure.com/subscriptions/xxxxxx-xxxxx-xxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Logic/workflows/xxxxxxx",
                "apiVersion": "2015-02-01-preview",
                "trigger": {
                    "name": "submitOrder",
                    "outputs": {
                        "body": "@repeatItem()"
                    }
                },
                "authentication": {
                    "type": "Basic",
                    "username": "default",
                    "password": "xxxxxxxxxxxxxx"
                }
            }
        },
        "sendInvoices": {
            "repeat": "@outputs('iterateOverOrders').repeatItems",
            "type": "Http",
            "inputs": {
                "uri": "http://www.example.com/?invoiceID=@{repeatOutputs().run.outputs.deliverTime.value}",
                "method": "GET"
            }
        }
    },
    "outputs": {}
}
```

그 후 자식 논리 앱에서 [`triggerBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerBody) 함수를 사용하여 자식 워크플로로 전달된 값을 가져오게 됩니다. 그러면 부모 흐름에 반환하고자 하는 데이터로 출력을 채우게 됩니다.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "calulatePrice": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?action=calcPrice&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
            }
        },
        "calculateDeliveryTime": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?action=calcTime&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
            }
        }
    },
    "outputs": {
        "deliverTime": {
            "type": "String",
            "value": "@outputs('calculateDeliveryTime').headers.etag"
        }
    }
}
```

[MSDN의 논리 앱 유형 작업](https://msdn.microsoft.com/ko-KR/library/azure/dn948511.aspx)에 대해 읽을 수 있습니다.

>[AZURE.NOTE]논리 앱 디자이너는 논리 앱 유형 작업을 지원하지 않으므로 정의를 수동으로 편집해야 합니다.


## 문제가 발생한 경우의 오류 처리 단계

일반적으로 하나 이상의 호출이 실패한 **경우에 한해** 일부 논리를 실행하는 *재구성 단계*를 쓸 수 있기를 원합니다. 이 예제에서는 다양한 위치에서 데이터를 가져오지만 호출이 실패한 경우에는 나중에 오류를 추적할 수 있는 곳에 메시지를 게시하고자 합니다.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/ko-KR/default.aspx",
                "https://gibberish.gibberish/"
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "repeat": "@parameters('dataFeeds')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem()"
            }
        },
        "postToErrorMessageQueue": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?noteAnErrorFor=@{repeatItem().inputs.uri}"
            },
            "conditions": [
                {
                    "expression": "@equals(actions('readData').status, 'Failed')"
                },
                {
                    "expression": "@equals(repeatItem().status, 'Failed')"
                }
            ]
        }
    },
    "outputs": {}
}
```

첫 번째 단계에서 목록을 반복하는 중이므로 두 개의 조건을 사용합니다. 단일 작업만 있는 경우에는 하나의 조건(첫 번째 조건)만 필요합니다. 또한 실패한 URL을 두 번째 단계로 전달하는 재구성 단계에서 실패한 작업에 *입력*을 사용할 수 있음도 기억하십시오.

![재구성](./media/app-service-logic-author-definitions/remediation.png)

마지막으로, 이제 오류를 처리했으므로 더 이상 실행을 **실패**로 표시하지 않습니다. 여기서 볼 수 있듯이, 비록 하나의 단계가 실패했더라도 이 오류를 처리하는 단계를 작성했기 때문에 이 실행은 **성공**했습니다.

## 병렬로 실행하는 둘 이상의 단계

직렬이 아닌 병렬로 복수의 작업을 실행하려면 해당 두 작업을 연결하는 `dependsOn` 조건을 제거해야 합니다. 일단 종속성이 제거되면 서로의 데이터가 필요한 경우가 아닌 이상 작업은 자동으로 병렬로 실행됩니다.

![분기](./media/app-service-logic-author-definitions/branches.png)

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/ko-KR/default.aspx",
                "https://office.live.com/start/default.aspx"
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "repeat": "@parameters('dataFeeds')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem()"
            }
        },
        "branch1": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?branch1Logic=@{repeatItem().inputs.uri}"
            }
        },
        "branch2": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?branch2Logic=@{repeatItem().inputs.uri}"
            }
        }
    },
    "outputs": {}
}
```

위 예제에서 볼 수 있듯이 branch1 및 branch2는 readData의 콘텐츠에 의해서만 좌우됩니다. 결과적으로 이 분기들은 모두 병렬로 실행됩니다.

![병렬](./media/app-service-logic-author-definitions/parallel.png)

두 분기에 대한 타임 스탬프는 동일함을 볼 수 있습니다.

## 논리의 두 조건부 분기 조인

두 분기에서 데이터를 받는 단일 작업을 사용하여 (실행되었을 수도 있고 실행되지 않았을 수도 있는) 논리의 두 개의 조건부 흐름을 결합할 수 있습니다.

이에 대한 전략은 하나의 항목을 처리하는지 항목의 컬렉션을 처리하는지에 따라 달라집니다. 단일 항목의 이 경우에는 [`coalesce()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#coalesce) 함수를 사용하고자 합니다.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
    "triggers": {},
    "actions": {
        "handleNormalOrders": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderNormally=@{parameters('order').id}"
            },
            "conditions": [
                {
                    "expression": "@lessOrEquals(parameters('order').quantity, 100)"
                }
            ]
        },
        "handleSpecialOrders": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderSpecially=@{parameters('order').id}"
            },
            "conditions": [
                {
                    "expression": "@greater(parameters('order').quantity, 100)"
                }
            ]
        },
        "submitInvoice": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?invoice=@{coalesce(outputs('handleNormalOrders')?.headers?.etag,outputs('handleSpecialOrders')?.headers?.etag )}"
            },
            "conditions": [
                {
                    "expression": "@or(equals(actions('handleNormalOrders').status, 'Succeeded'), equals(actions('handleSpecialOrders').status, 'Succeeded'))"
                }
            ]
        }
    },
    "outputs": {}
}
```
 
또는 첫 두 분기가 모두 명력 목록에서 작동하는 경우, 예를 들면 [`union()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#union) 함수를 사용하여 두 분기의 데이터를 결합하고자 합니다.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "orders": {
            "defaultValue": [
                {
                    "quantity": 10,
                    "id": "myorder1"
                },
                {
                    "quantity": 200,
                    "id": "specialOrder"
                },
                {
                    "quantity": 5,
                    "id": "myOtherOrder"
                }
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "handleNormalOrders": {
            "repeat": "@parameters('orders')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderNormally=@{repeatItem().id}"
            },
            "conditions": [
                {
                    "expression": "@lessOrEquals(repeatItem().quantity, 100)"
                }
            ]
        },
        "handleSpecialOrders": {
            "repeat": "@parameters('orders')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderSpecially=@{repeatItem().id}"
            },
            "conditions": [
                {
                    "expression": "@greater(repeatItem().quantity, 100)"
                }
            ]
        },
        "submitInvoice": {
            "repeat": "@union(actions('handleNormalOrders').outputs.repeatItems, actions('handleSpecialOrders').outputs.repeatItems)",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?invoice=@{repeatOutputs().headers.etag}"
            },
            "conditions": [
                {
                    "expression": "@equals(repeatItem().status, 'Succeeded')"
                }
            ]
        }
    },
    "outputs": {}
}
```
## 문자열을 사용한 작업

문자열을 조작하는 데 사용할 수 있는 다양한 기능이 있습니다. 시스템에 전달하고자 하는 문자열이 있지만 문자 인코딩이 적절하게 처리될지 확신할 수 없는 경우를 예로 들어 봅니다. 하나의 옵션은 이 문자열을 base64 인코딩하는 것입니다. 그러나 URL에서 이스케이프를 방지하기 위해 몇 문자를 대체할 것입니다.

또한 첫 5개 문자는 사용되지 않으므로 명령 이름의 하위 문자열을 원합니다.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1",
                "orderer": "NAME=Stèphén__Šīçiłianö"
            },
            "type": "Object"
        }
    },
    "triggers": {},
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

안팎을 뒤집어 작업하기:

1. 명령자 이름의 [`length()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#length)을(를) 가져오면 전체 문자 수를 다시 반환합니다.

2. (더 짧은 문자열을 원하므로) 5 빼기

3. 실제로 [`substring()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#substring)을(를) 가져옵니다. 인덱스 `5`에서 시작하여 문자열의 나머지로 이동합니다.

4. 이 하위 문자열을 [`base64()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#base64) 문자열로 변환

5. [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) 모든 `+` 문자를 `-`(으)로

6. [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) 모든 `/` 문자를 `_`(으)로

## 날짜 시간을 사용한 작업

날짜 시간은 자연스럽게 **트리거**를 지원하지 않는 데이터 소스에서 데이터를 가져오려고 할 때 특히 유용할 수 있습니다. 날짜 시간은 다양한 단계에 소용되는 시간을 파악하는 데에도 사용할 수 있습니다.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
    "triggers": {},
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{parameters('order').id}"
            }
        },
        "timingWarning": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
        	},
            "conditions": [
                {
                    "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
                }
            ]
        }
    },
    "outputs": {}
}
```

이 예제에서는 이전 단계의 `startTime`을(를) 추출합니다. 그 후에 현재 시간 및 1초를 뺀 시간을 가져옵니다: [`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/dn948512.aspx#addseconds)(`minutes` 또는 `hours`와 같은 다른 시간 단위를 사용할 수 있음) 마지막으로 이 두 값을 비교할 수 있습니다. 첫 번째 값이 두 번째보다 작은 경우에는 명령이 첫 번째 위치으면 1초 이상의 시간이 경과되었음을 의미합니다.

문자열 포맷터를 사용하여 날짜를 포맷할 수 있음도 기억하십시오. 쿼리 스트링에서는 [`utcnow('r')`](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow)을(를) 사용하여 RFC1123을 가져왔습니다. 전체 데이터 포맷은 [MSDN에 설명되어 있습니다](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow).

## 런타임에 값을 전달하여 동작을 다양화

논리 앱을 시작하는 데 사용하는 일부 값에 기반하여 실행하고자 하는 다른 동작이 있는 경우를 예로 들어 봅니다. [`triggerOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerOutputs) 함수를 사용하여 전달받은 중에서 이 값을 얻을 수 있습니다.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "triggers": {},
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@triggerOutputs().uriToGet"
            }
        },
        "extraStep": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/extraStep"
            },
            "conditions": [
                {
                    "expression": "@triggerOutputs().doMoreLogic"
                }
            ]
        },  
    },
    "outputs": {}
}
```

실제로 이 작업을 하기 위해서는 실행을 시작했을 때 원하는 속성을 전달해야 합니다(위의 예제 `uriToGet` 및 `doMoreLogic`에서). [기본 인증을 사용](https://msdn.microsoft.com/library/azure/dn948513.aspx#basicAuth)할 수 있는 호출은 다음과 같습니다.

```
POST https://<<Logic app endpoint from the Essentials>>/run?api-version=2015-02-01-preview
Authorization: Basic <<Based 64 encoded username (default) : password (from the Settings blade)>>
Content-type: application/json
```

다음 페이로드를 사용합니다. 이제 사용할 값의 논리 앱이 제공되었음을 기억하십시오.

```
{
    "outputs": {
        "uriToGet" : "http://my.uri.I.want/",
        "doMoreLogic" : true
    }
}
``` 

이 논리 앱은 실행 시에 전달받은 URI를 호출하고, `true`을 전달하였으므로 해당 추가 단계를 실행합니다. 배포 시에 매개 변수를 다양화하기만 원하는 경우(*매 실행*마다가 아님), 아래와 같이 호출하여 `parameters`을(를) 사용해야 합니다.

## 다른 환경에 대해 배포 시 매개 변수 사용

배포 환경, 스테이징 환경 및 프로덕션 환경을 가지는 배포 주기가 있는 것이 일반적입니다. 이 모두에서 예를 들어, 동일한 정의를 원하지만 다른 데이터베이스를 사용할 수 있습니다. 마찬가지로, 높은 가용성을 위해 많은 다양한 하위 지역에 걸쳐 동일한 정의를 사용하고자 하지만 해당 하위 지역의 데이터베이스와 통신하는 각각의 논리 앱 인스턴스를 원할 수 있습니다.

이는 위와 같이 호출하여 사용해야 하는 `trigger()` 함수에 대해 *런타임*에 다른 매개 변수를 가져오는 것과는 다름을 기억하십시오.

다음과 같은 매우 단순한 정의로 시작할 수 있습니다.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "connection": {
            "type": "string"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('connection')"
            }
        }        
    },
    "outputs": {}
}
```

그 후 논리 앱에 대한 실제 `PUT` 요청에서 매개 변수 `connection`를 제공할 수 있습니다. 더 이상의 기본값이 없으면 이 매개 변수는 논리 앱 페이로드에 필요하다는 점을 기억하십시오.

```
{
    "properties": {
        "sku": {
            "name": "Premium",
            "plan": {
                "id": "/subscriptions/xxxxx/resourceGroups/xxxxxx/providers/Microsoft.Web/serverFarms/xxxxxx"
            }
        },
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

그러면 각 환경에서 `connection` 매개 변수에 대해 다른 값을 제공할 수 있습니다.

## 조건이 충족될 때까지 단계 실행

호출 중인 API가 있을 수 있으며 특정 응답을 기다렸다가 진행하려 할 수 있습니다. 예를 들어, 다른 사용자가 디렉터리에 파일을 업로드할 때까지 기다렸다가 파일을 처리한다고 가정해 보겠습니다. *do-until*로 이 작업을 수행할 수 있습니다.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "http0": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://mydomain/listfiles"
            },
            "until": {
                "limit": {
                    "timeout": "PT10M"
                },
                "conditions": [
                    {
                        "expression": "@greater(length(action().outputs.body),0)"
                    }
                ]
            }
        }
    },
    "outputs": {}
}
```

논리 앱 만들기 및 관리에 대한 가능한 모든 옵션은 [REST API 설명서](https://msdn.microsoft.com/library/azure/dn948513.aspx)를 참조하십시오.

<!---HONumber=Sept15_HO2-->