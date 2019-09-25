---
title: 리소스 관리자 템플릿을 사용 하 여 Azure에서 진단 설정 만들기 | Microsoft Docs
description: 리소스 관리자 템플릿을 사용 하 여 진단 설정을 만들어 Azure Monitor 로그, Azure storage 또는 Azure Event Hubs에 Azure platform logs를 전달 합니다.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: f65e3c4f9582fcc5c28412d44e513fa6bcb9e870
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262337"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용 하 여 Azure에서 진단 설정 만들기
Azure의 [플랫폼 로그](platform-logs-overview.md) 는 azure 리소스 및 해당 리소스가 종속 된 azure 플랫폼에 대 한 자세한 진단 및 감사 정보를 제공 합니다. 이 문서에서는 [Azure Resource Manager 템플릿](../../azure-resource-manager/resource-group-authoring-templates.md) 사용에 대 한 세부 정보를 제공 하 여 플랫폼 로그를 다른 대상으로 수집 하도록 진단 설정을 구성 합니다. 이렇게 하면 리소스를 만들 때 플랫폼 로그 수집을 자동으로 시작할 수 있습니다.


## <a name="resource-manager-template"></a>Resource Manager 템플릿
리소스 관리자 템플릿에는 진단 설정을 만들기 위해 편집 해야 하는 두 섹션이 있습니다. 이러한 섹션은 다음 섹션에 설명 되어 있습니다.

### <a name="parameters"></a>매개 변수
진단 설정의 [대상](diagnostic-settings.md#destinations) 에 따라 저장소 계정 이름, 이벤트 허브 권한 부여 규칙 id 및 Log Analytics 작업 영역 id에 대 한 매개 변수 blob에 매개 변수를 추가 합니다.
   
```json
"settingName": {
  "type": "string",
  "metadata": {
    "description": "Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
  }
},
"storageAccountName": {
  "type": "string",
  "metadata": {
    "description": "Name of the Storage Account in which platform logs should be saved."
  }
},
"eventHubAuthorizationRuleId": {
  "type": "string",
  "metadata": {
    "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
  }
},
"eventHubName": {
  "type": "string",
  "metadata": {
    "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
  }
},
"workspaceId":{
  "type": "string",
  "metadata": {
    "description": "Azure Resource ID of the Log Analytics workspace for the Log Analytics workspace to which logs will be sent."
  }
}
```

### <a name="resources"></a>리소스
진단 설정을 만들 리소스의 리소스 배열에서 형식의 `[resource namespace]/providers/diagnosticSettings`리소스를 추가 합니다. 속성 섹션은 [진단 설정-만들기 또는 업데이트](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate)에 설명 된 형식을 따릅니다. [리소스가 메트릭을 지 원하는](metrics-supported.md)경우 속성을추가하여리소스메트릭을동일한대상으로수집합니다.`metrics`
   
```json
"resources": [
  {
    "type": "providers/diagnosticSettings",
    "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
    "dependsOn": [
      "[/*resource Id for which resource logs will be enabled>*/]"
    ],
    "apiVersion": "2017-05-01-preview",
    "properties": {
      "name": "[parameters('settingName')]",
      "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
      "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
      "eventHubName": "[parameters('eventHubName')]",
      "workspaceId": "[parameters('workspaceId')]",
      "logs": [ 
        {
          "category": "/* log category name */",
          "enabled": true,
          "retentionPolicy": {
            "days": 0,
            "enabled": false
          }
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true,
          "retentionPolicy": {
            "enabled": false,
            "days": 0
          }
        }
      ]
    }
  }
]
```



## <a name="example"></a>예제
다음은 논리 앱을 만들고 저장소 계정에서 이벤트 허브 및 저장소로 리소스 로그를 스트리밍할 수 있도록 하는 진단 설정을 만드는 전체 예제입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "logicAppName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Logic App that will be created."
      }
    },
    "testUri": {
      "type": "string",
      "defaultValue": "https://azure.microsoft.com/status/feed/"
    },
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name of the setting. Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which resource logs should be saved."
      }
    },
    "eventHubAuthorizationRuleId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
      }
    },
    "eventHubName": {
      "type": "string",
      "metadata": {
        "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Logic/workflows",
      "name": "[parameters('logicAppName')]",
      "apiVersion": "2016-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
          ],
          "apiVersion": "2017-05-01-preview",
          "properties": {
            "name": "[parameters('settingName')]",
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "WorkflowRuntime",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ],
            "metrics": [
              {
                "timeGrain": "PT1M",
                "enabled": true,
                "retentionPolicy": {
                  "enabled": false,
                  "days": 0
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```


## <a name="next-steps"></a>다음 단계
* 자세한 내용은 [Azure의 플랫폼 로그를](platform-logs-overview.md)참조 하세요.
* [진단 설정](diagnostic-settings.md)에 대해 알아봅니다.