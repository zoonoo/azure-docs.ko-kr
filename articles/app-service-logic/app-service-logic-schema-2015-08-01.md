<properties 
	pageTitle="새 스키마 버전 2015-08-01-preview" 
	description="최신 버전의 논리 앱에 대한 JSON 정의 작성 방법 알아보기" 
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
	ms.date="02/17/2016"
	ms.author="stepsic"/>
	
# 새 스키마 버전 2015-08-01-preview

논리 앱에 대한 새 스키마 및 API 버전에 논리 앱의 안정성 및 사용 편의성을 개선하는 다양한 향상된 기능이 있습니다. 4개의 주요 차이점이 있습니다.

1. **APIApp** 동작 유형이 새로운 **APIConnection** 동작 유형으로 업데이트되었습니다.
2. **Repeat**의 이름이 **Foreach**로 바뀌었습니다.
3. **HTTP 수신기** API 앱은 더 이상 필요하지 않습니다.
4. 하위 워크플로 호출에 새 스키마를 사용합니다.

## 1\. API 연결로 이동

가장 큰 변화는 API를 사용하기 위해 API 앱을 Azure 구독에 더 이상 배포하지 않아도 된다는 점입니다. * 관리 API * 사용자 지정 Web API라는 두 가지 방법으로 API를 사용할 수 있습니다.

이들 각각은 관리 및 호스팅 모델이 다르므로 약간 다르게 처리됩니다. 이 모델의 장점은 사용자의 리소스 그룹에 배포되는 리소스로 더 이상 제약되지 않는다는 점입니다.

### 관리 API

Office 365, Salesforce, Twitter, FTP 등 Microsoft가 사용자를 대신하여 관리하는 여러 API가 있습니다. 이러한 일부 관리 API는 Bing Translate처럼 있는 그대로 사용할 수 있는 반면 구성이 필요한 것도 있습니다. 이 구성을 *연결*이라고 합니다.

예를 들어 Office 365를 사용하는 경우 Office 365 로그인 토큰을 포함하는 연결을 만들어야 합니다. 이 토큰은 논리 앱에서 항상 Office 365 API를 호출할 수 있도록 안전하게 저장되어 새로 고침됩니다. 또는 SQL이나 FTP 서버에 연결하려는 경우 연결 문자열이 있는 연결을 만들어야 합니다.

정의 내에서 이러한 작업을 `APIConnection`이라고 합니다. 다음은 전자 메일을 보내도록 Office 365를 호출하는 연결에 대한 예입니다.

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

API 연결에 고유한 입력 부분은 `host` 개체입니다. 여기에는 `api` 및 `connection`의 두 부분이 포함됩니다.

`api`에는 관리 API가 호스팅되는 런타임 URL이 포함됩니다. `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`를 호출하여 사용 가능한 모든 관리 API를 볼 수 있습니다.

API를 사용하는 경우 **연결 매개 변수**가 정의되어 있거나 그렇지 않을 수 있습니다. 정의되지 않은 경우 **연결**이 필요하지 않습니다. 정의된 경우 연결을 만들어야 합니다. 해당 연결을 만들 때 선택한 이름을 포함한 후 `host` 개체 내부 `connection` 개체에서 참조합니다. 리소스 그룹에서 연결을 만들려면 다음을 호출합니다.

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

다음 본문을 포함합니다.


```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
	"parameterValues" : {
		"accountName" : "{The name of the storage account -- the set of parameters is different for each API}"
	}
  },
  "location" : "{Logic app's location}"
}
```

### Azure Resource Manager 템플릿에서 관리 API 배포

대화형 로그인이 필요하지 않다면 ARM 템플릿에서 전체 응용 프로그램을 만들 수 있습니다. 로그인이 필요한 경우 ARM 템플릿을 사용하여 모든 항목을 설정할 수 있지만 포털에 방문하여 연결에 권한을 부여해야 합니다.

```
	"resources": [{
		"apiVersion": "2015-08-01-preview",
		"name": "azureblob",
		"type": "Microsoft.Web/connections",
		"location": "[resourceGroup().location]",
		"properties": {
			"api": {
				"id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
			},
			"parameterValues": {
				"accountName": "[parameters('storageAccountName')]",
				"accessKey": "[parameters('storageAccountKey')]"
			}
		}
	}, {
		"type": "Microsoft.Logic/workflows",
		"apiVersion": "2015-08-01-preview",
		"name": "[parameters('logicAppName')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[resourceId('Microsoft.Web/connections', 'azureblob')]"
		],
		"properties": {
			"sku": {
				"name": "[parameters('sku')]",
				"plan": {
					"id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
				}
			},
			"definition": {
				"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
				"actions": {
					"Create_file": {
						"type": "apiconnection",
						"inputs": {
							"host": {
								"api": {
									"runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
								},
								"connection": {
									"name": "@parameters('$connections')['azureblob']['connectionId']"
								}
							},
							"method": "post",
							"queries": {
								"folderPath": "[concat('/',parameters('containerName'))]",
								"name": "helloworld.txt"
							},
							"body": "@decodeDataUri('data:,Hello+world!')",
							"path": "/datasets/default/files"
						},
						"conditions": []
					}
				},
				"contentVersion": "1.0.0.0",
				"outputs": {},
				"parameters": {
					"$connections": {
						"defaultValue": {},
						"type": "Object"
					}
				},
				"triggers": {
					"recurrence": {
						"type": "Recurrence",
						"recurrence": {
							"frequency": "Day",
							"interval": 1
						}
					}
				}
			},
			"parameters": {
				"$connections": {
					"value": {
						"azureblob": {
							"connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
							"connectionName": "azureblob",
							"id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
						}

					}
				}
			}
		}
	}]
```

이 예에서 해당 연결은 리소스 그룹에서 작동하는 일반 리소스일 뿐임을 알 수 있습니다. 연결은 구독에서 사용할 수 있는 관리 API를 참조합니다.

### 사용자 지정 Web API

사용자 고유의 API(특히, Microsoft 관리 항목)를 사용하는 경우 기본 제공 **HTTP** 작업을 사용하여 호출해야 합니다. 이상적인 환경을 위해서는 API에 대한 swagger 끝점을 노출해야 합니다. 이렇게 하면 논리 앱 디자이너가 API에 대한 입력 및 출력을 렌더링할 수 있습니다. swagger가 없는 경우 디자이너는 입력 및 출력을 불투명 JSON 개체로 표시할 수 있게 됩니다.

다음은 새 `metadata.apiDefinitionUrl` 속성을 보여주는 예입니다. ```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata" : {
              "apiDefinitionUrl" : "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method" : "GET"
            }
        }
    }
}
```

**앱 서비스**에서 Web API를 호스팅하는 경우 디자이너에서 사용 가능한 작업 목록이 자동으로 표시됩니다. 그렇지 않으면 URL에 직접 붙여넣어야 합니다. Swagger에서 지원되는 어떤 방법으로든 API 자체를 보호할 수 있지만 Swagger 끝점을 논리 앱 디자이너 내부에서 사용할 수 없도록 하려면 끝점을 인증하지 않아야 합니다.

### 2015-08-01-preview와 함께 이미 배포된 API 앱 사용

이전에 API 앱을 배포한 경우 **HTTP** 작업을 통해 호출할 수 있습니다.

예를 들어 Dropbox를 사용하여 파일을 나열하는 경우 **2014-12-01-preview** 스키마 버전 정의에 다음과 같은 항목이 포함될 수 있습니다. ```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

아래와 같은 동등한 HTTP 작업을 생성할 수 있습니다(논리 앱 정의의 매개 변수 섹션은 변경되지 않고 유지됩니다).

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata" : {
              "apiDefinitionUrl" : "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method" : "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

이러한 속성을 하나씩 연습합니다.

| 작업 속성 | 설명 |
| --------------- | -----------  |
| `type` | `APIapp` 대신 `Http` |
| `metadata.apiDefinitionUrl` | 논리 앱 디자이너에서 이 작업을 사용하려는 경우 메타데이터 끝점을 포함하려고 합니다. 다음에서 생성됩니다. `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | 다음에서 생성됩니다. `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | 항상 `POST` |
| `inputs.body` | api 앱 매개 변수와 동일 | 
| `inputs.authentication` | api 앱 인증과 동일 |

이 방법은 모든 API 앱 작업에 대해 작동합니다. 그러나 이러한 이전 API 앱은 더 이상 지원되지 않으며 위의 다른 두 옵션 중 하나로 전환해야 함을 유의하세요(관리 API 또는 사용자 지정 Web API 호스팅).

## 2\. Repeat가 Foreach로 이름 변경됨

이전 스키마 버전에서 **Repeat**가 혼동되고 실제로 for each loop인지 제대로 포착되지 않는다는 의견을 많이 받았습니다. 따라서 **Foreach**로 이름을 변경하였습니다. 예:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

이제 다음과 같이 작성합니다.

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

이전에 함수 `@repeatItem()`은 반복되는 현재 항목을 참조하는 데 사용되었습니다. 이것이 `@item()`으로 간소화되었습니다.

### Foreach의 출력 참조
더욱 간소화하기 위해 **Foreach** 작업의 출력은 **repeatItems**라는 개체에 래핑되지 않습니다. 따라서 위의 repeat 출력은 다음과 같지만

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang="en" xml:lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:Web="http://schemas.live.com/Web/">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

이제 다음과 같습니다.

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang="en" xml:lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:Web="http://schemas.live.com/Web/">...</html>"
        }
        "status": "Succeeded"
    }
]
```

이러한 출력을 참조할 때 작업 본문을 가져오려면 다음을 수행해야 했습니다.

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "repeat" : "@outputs('pingBing').repeatItems",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@repeatItem().outputs.body"
            }
        }
    }
}
```

이제 대신 다음을 수행하면 됩니다.

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "foreach" : "@outputs('pingBing')",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@item().outputs.body"
            }
        }
    }
}
```

이러한 변경으로 함수 `@repeatItem()`, `@repeatBody()` 및 `@repeatOutputs()`가 제거됩니다.

## 3\. 네이티브 HTTP 수신기 
HTTP 수신기 기능은 기본 제공되므로 더 이상 HTTP 수신기 API 앱을 배포할 필요가 없습니다. [논리 앱 끝점을 호출 가능한 상태로 만드는 방법에 대한 자세한 내용은 여기](app-service-logic-http-endpoint.md)를 읽어 보세요.

이러한 변경으로 함수 `@accessKeys()`가 제거되고 끝점을 가져오기 위해 `@listCallbackURL()` 함수로 대체되었습니다(필요한 경우). 또한 이제 논리 앱에서 트리거를 하나 이상 정의해야 합니다. 워크플로를 `/run`하려는 경우 `manual`, `apiConnectionWebhook` 또는 `httpWebhook` 트리거 중 하나를 포함해야 합니다.

## 4\. 하위 워크플로 호출

이전에는 하위 워크플로를 호출하려면 해당 워크플로로 이동하여 액세스 토큰을 가져온 후 해당 하위를 호출하려는 논리 앱 정의에 붙여넣어야 했습니다. 새 스키마 버전을 사용할 경우 논리 앱 엔진은 런타임에 하위 워크플로에 대한 SAS를 자동으로 생성합니다. 따라서 정의에 어떠한 암호도 붙여넣을 필요가 없습니다. 다음은 예제입니다.

```
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName" : "myendpointtrigger"
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
    "conditions" : []
}
```

두 번째 향상된 내용은 들어오는 요청에 대한 전체 액세스 권한을 하위 워크플로에 부여하는 것입니다. 따라서 *queries* 섹션 및 *headers* 개체에서 매개 변수를 전달할 수 있으며 전체 본문을 완전히 정의할 수 있습니다.

마지막으로, 하위 워크플로에 필요한 변경 내용이 있습니다. 이전에는 하위 워크플로를 직접 호출할 수 있었지만 이제는 상위에서 호출할 워크플로에 트리거 끝점을 정의해야 합니다. 일반적으로 **manual** 유형의 트리거를 추가한 후 상위 정의에 사용합니다. 특히 호출 중인 트리거를 항상 지정해야 하므로 `host` 속성은 `triggerName`을 포함합니다.

## 기타 변경 내용

### 새 쿼리 속성
이제 모든 작업 유형에서 **queries**라는 새 입력을 지원합니다. 문자열을 직접 조합하는 대신 구조화된 개체일 수 있습니다.

### parse() 함수 이름 변경됨
곧 더 많은 콘텐츠 유형이 추가될 예정이므로 `parse()` 함수의 이름이 `json()`으로 변경되었습니다.

## 서비스 예정: 엔터프라이즈 통합 API
현재는 관리된 버전의 엔터프라이즈 통합 API가 아직 없습니다(예: AS2). [로드맵](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/)에 나와 있는 대로 곧 서비스 예정입니다. 그 동안은 위의 "이미 배포된 API 앱 사용"에 설명된 대로 HTTP 작업을 통해 기존에 배포된 BizTalk API를 사용하면 됩니다.

<!---HONumber=AcomDC_0224_2016-->