<properties 
	pageTitle="작성자 논리 앱 정의 | Microsoft Azure" 
	description="논리 앱에 대한 JSON 정의 작성 방법을 알아봅니다." 
	authors="jeffhollan" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jehollan"/>
	
# 작성자 논리 앱 정의
이 항목에서는 간단하고 선언적인 JSON 언어인 [Azure Logic Apps](app-service-logic-what-are-logic-apps.md) 정의를 사용하는 방법을 설명합니다. 아직 [새 논리 앱 만드는 방법](app-service-logic-create-a-logic-app.md)을 확인하지 않았다면 먼저 확인하십시오. [MSDN 정의 언어의 전체 참조 자료](http://aka.ms/logicappsdocs)를 읽을 수도 있습니다.

## 목록에서 반복되는 여러 단계

[foreach 형식](app-service-logic-loops-and-scopes.md) 항목을 활용하여 최대 10,000개의 항목 배열을 반복하고 각각에 대해 작업을 수행할 수 있습니다.

## 문제가 발생한 경우의 오류 처리 단계

일반적으로 하나 이상의 호출이 실패한 **경우에 한해** 일부 논리를 실행하는 *재구성 단계*를 쓸 수 있기를 원합니다. 이 예제에서는 다양한 위치에서 데이터를 가져오지만 호출이 실패한 경우에는 나중에 오류를 추적할 수 있는 곳에 메시지를 게시하고자 합니다.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
	},
	"triggers": {
		"manual": {
			"type": "manual"
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
				"readData": ["Failed"]
			}
		}
	},
	"outputs": {}
}
```

`runAfter` 속성을 이용하여 지정할 수 있으며, `postToErrorMessageQueue`은(는) `readData`이(가) **실패**한 이후에만 실행해야 합니다. 또한 이는 가능한 값의 목록이 될 수 있기 때문에 `runAfter`은(는) `["Succeeded", "Failed"]`이(가) 될 수 있습니다.

마지막으로, 이제 오류를 처리했으므로 더 이상 실행을 **실패**로 표시하지 않습니다. 여기서 볼 수 있듯이, 비록 하나의 단계가 실패했더라도 이 오류를 처리하는 단계를 작성했기 때문에 이 실행은 **성공**했습니다.

## 병렬로 실행하는 둘 이상의 단계

여러 작업을 병렬로 실행하려면 `runAfter` 속성은 런타임 시 동일해야 합니다.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {},
	"triggers": {
		"manual": {
			"type": "manual"
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
			"inputs": "...",
			"runAfter": {
				"readData": ["Succeeded"]
			}
		},
		"branch2": {
			"type": "Http",
			"inputs": "...",
			"runAfter": {
				"readData": ["Succeeded"]
			}
		}
	},
	"outputs": {}
}
```

모두 위의 예에서 볼 수 있듯이 `branch1` 및 `branch2`은(는) `readData` 이후 실행되도록 설정되어 있습니다. 결과적으로 이 분기들은 모두 병렬로 실행됩니다.

![병렬](./media/app-service-logic-author-definitions/parallel.png)

두 분기에 대한 타임 스탬프는 동일함을 볼 수 있습니다.

## 두 병렬 분기의 조인

위와 유사한 `runAfter` 속성에 항목을 추가하여 병렬로 실행하도록 설정된 두 가지 작업을 조인할 수 있습니다.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
    "actions": {
        "readData": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        },
        "branch1": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "branch2": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "join": {
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
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {},
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {}
            },
            "kind": "Http",
            "type": "Request"
        }
    }
}
```

![병렬](./media/app-service-logic-author-definitions/join.png)

## 일부 다른 구성에는 목록에서 항목 매핑

다음으로 속성의 값에 따라 완전히 다른 콘텐츠를 가져오는 경우를 다루겠습니다. 매개 변수를 대상으로 하는 값의 맵을 만들 수 있습니다.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"specialCategories": {
			"defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
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
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
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
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
			},
			"conditions": [{
				"expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
			}],
			"forEach": "@body('getArticles').responseData.feed.entries"
		}
	}
}
```

이 경우, 먼저 문서 목록을 가져온 후 매개 변수로 정의된 카테고리에 기반하여 두 번째 단계가 맵에서 콘텐츠를 가져 올 URL을 조회합니다.

여기서는 다음 두 항목에 주의해야 합니다. [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) 함수는 카테고리가 정의된 알려진 카테고리 중 하나에 일치하는지 확인하는 데 사용됩니다. 둘째로 일단 카테고리를 가져오면 대괄호를 사용하여 맵의 항목을 끌어올 수 있습니다: `parameters[...]`

## 문자열을 사용한 작업

문자열을 조작하는 데 사용할 수 있는 다양한 기능이 있습니다. 시스템에 전달하고자 하는 문자열이 있지만 문자 인코딩이 적절하게 처리될지 확신할 수 없는 경우를 예로 들어 봅니다. 하나의 옵션은 이 문자열을 base64 인코딩하는 것입니다. 그러나 URL에서 이스케이프를 방지하기 위해 몇 문자를 대체할 것입니다.

또한 첫 5개 문자는 사용되지 않으므로 명령 이름의 하위 문자열을 원합니다.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
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
	"triggers": {
		"manual": {
			"type": "manual"
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

안팎을 뒤집어 작업하기:

1. 명령자 이름의 [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length)을(를) 가져오면 전체 문자 수를 다시 반환합니다.

2. (더 짧은 문자열을 원하므로) 5 빼기

3. 실제로 [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring)을(를) 가져옵니다. 인덱스 `5`에서 시작하여 문자열의 나머지로 이동합니다.

4. 이 하위 문자열을 [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) 문자열로 변환

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) 모든 `+` 문자를 `-`(으)로

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) 모든 `/` 문자를 `_`(으)로

## 날짜 시간을 사용한 작업

날짜 시간은 자연스럽게 **트리거**를 지원하지 않는 데이터 소스에서 데이터를 가져오려고 할 때 특히 유용할 수 있습니다. 날짜 시간은 다양한 단계에 소용되는 시간을 파악하는 데에도 사용할 수 있습니다.

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
		"manual": {
			"type": "manual"
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
		"timingWarning": {
			"actions" {
				"type": "Http",
				"inputs": {
					"method": "GET",
					"uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
				},
				"runAfter": {}
			}
			"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
		}
	},
	"outputs": {}
}
```

이 예제에서는 이전 단계의 `startTime`을(를) 추출합니다. 그 후에 현재 시간 및 1초를 뺀 시간을 가져옵니다: [`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds)(`minutes` 또는 `hours`와 같은 다른 시간 단위를 사용할 수 있음) 마지막으로 이 두 값을 비교할 수 있습니다. 첫 번째 값이 두 번째보다 작은 경우에는 명령이 첫 번째 위치으면 1초 이상의 시간이 경과되었음을 의미합니다.

문자열 포맷터를 사용하여 날짜를 포맷할 수 있음도 기억하십시오. 쿼리 스트링에서는 [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow)을(를) 사용하여 RFC1123을 가져왔습니다. 전체 데이터 포맷은 [MSDN에 설명되어 있습니다](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow).

## 다른 환경에 대해 배포 시 매개 변수 사용

배포 환경, 스테이징 환경 및 프로덕션 환경을 가지는 배포 주기가 있는 것이 일반적입니다. 이 모두에서 예를 들어, 동일한 정의를 원하지만 다른 데이터베이스를 사용할 수 있습니다. 마찬가지로, 높은 가용성을 위해 많은 다양한 하위 지역에 걸쳐 동일한 정의를 사용하고자 하지만 해당 하위 지역의 데이터베이스와 통신하는 각각의 논리 앱 인스턴스를 원할 수 있습니다.

이는 위와 같이 호출하여 사용해야 하는 `trigger()` 함수에 대해 *런타임*에 다른 매개 변수를 가져오는 것과는 다름을 기억하십시오.

다음과 같은 매우 단순한 정의로 시작할 수 있습니다.

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
		"manual": {
			"type": "manual"
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

그 후 논리 앱에 대한 실제 `PUT` 요청에서 매개 변수 `uri`를 제공할 수 있습니다. 더 이상의 기본값이 없으면 이 매개 변수는 논리 앱 페이로드에 필요하다는 점을 기억하십시오.

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

그러면 각 환경에서 `connection` 매개 변수에 대해 다른 값을 제공할 수 있습니다.

논리 앱 만들기 및 관리에 대한 가능한 모든 옵션은 [REST API 설명서](https://msdn.microsoft.com/library/azure/mt643787.aspx)를 참조하십시오.

<!---HONumber=AcomDC_0727_2016-->