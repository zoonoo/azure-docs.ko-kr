---
title: 리소스 관리자 템플릿을 사용 하 여 Azure에서 진단 설정 만들기
description: 리소스 관리자 템플릿을 사용 하 여 진단 설정을 만들어 Azure Monitor 로그, Azure storage 또는 Azure Event Hubs에 Azure platform logs를 전달 합니다.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 2a171ae89e8314684eddf29f78b9b09bc52f9c9b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977549"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용 하 여 Azure에서 진단 설정 만들기
의 [진단 설정](diagnostic-settings.md) Azure Monitor azure 리소스 및 해당 리소스가 종속 된 azure 플랫폼에 의해 수집 되는 [플랫폼 로그](platform-logs-overview.md) 를 보낼 위치를 지정 합니다. 이 문서에서는 [Azure Resource Manager 템플릿을](../../azure-resource-manager/templates/template-syntax.md) 사용 하 여 다른 대상에 플랫폼 로그를 수집 하는 진단 설정을 만들고 구성 하는 방법에 대 한 자세한 내용과 예제를 제공 합니다.

> [!NOTE]
> 다른 Azure 리소스에 대 한 진단 설정 같은 PowerShell 또는 CLI를 사용 하 여 Azure 활동 로그에 대 한 [진단 설정을 만들](diagnostic-settings.md) 수 없으므로이 문서의 정보를 사용 하 여 활동 로그에 대 한 리소스 관리자 템플릿을 만들고 POWERSHELL 또는 CLI를 사용 하 여 템플릿을 배포 합니다.

## <a name="deployment-methods"></a>배포 방법
PowerShell 및 CLI를 비롯 한 유효한 메서드를 사용 하 여 리소스 관리자 템플릿을 배포할 수 있습니다. 활동 로그에 대 한 진단 설정은 CLI 용 `az deployment create` 또는 PowerShell 용 `New-AzDeployment`를 사용 하 여 구독에 배포 해야 합니다. 리소스 로그에 대 한 진단 설정은 CLI 용 `az group deployment create` 또는 PowerShell 용 `New-AzResourceGroupDeployment`를 사용 하 여 리소스 그룹에 배포 해야 합니다.

자세한 내용은 [리소스 관리자 템플릿을 사용 하 여 리소스 배포 및 Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) 및 [리소스 관리자 템플릿 및 Azure CLI를 사용 하 여 리소스](../../azure-resource-manager/templates/deploy-cli.md) 배포를 참조 하세요. 





## <a name="resource-logs"></a>리소스 로그
리소스 로그의 경우 `<resource namespace>/providers/diagnosticSettings` 형식의 리소스를 템플릿에 추가 합니다. 속성 섹션은 [진단 설정-만들기 또는 업데이트](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate)에 설명 된 형식을 따릅니다. 수집 하려는 리소스에 대해 유효한 각 범주에 대 한 `logs` 섹션에 `category`을 제공 합니다. [리소스가 메트릭을 지 원하는](metrics-supported.md)경우 리소스 메트릭을 동일한 대상으로 수집 하려면 `metrics` 속성을 추가 합니다.

다음은 Log Analytics 작업 영역, 저장소 계정 및 이벤트 허브에 대 한 특정 리소스의 리소스 로그 범주를 수집 하는 템플릿입니다.

```json
"resources": [
  {
    "type": "/<resource namespace>/providers/diagnosticSettings",
    "name": "[concat(parameters('resourceName'),'/microsoft.insights/', parameters('settingName'))]",
    "dependsOn": [
      "[<resource Id for which resource logs will be enabled>]"
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
          "category": "<category name>",
          "enabled": true
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true
        }
      ]
    }
  }
]
```



### <a name="example"></a>예
다음은 이벤트 허브, 저장소 계정 및 Log Analytics 작업 영역으로 리소스 로그를 스트리밍할 수 있도록 하는 자동 크기 조정 설정에 대 한 진단 설정을 만드는 예입니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "autoscaleSettingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the autoscale setting"
            }
        },
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceIDl of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
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
        }
    },
    "variables": {},
    "resources": [
    {
      "type": "microsoft.insights/autoscalesettings/providers/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[concat(parameters('autoscaleSettingName'),'/microsoft.insights/', parameters('settingName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/autoscalesettings', parameters('autoscaleSettingName'))]"
      ],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "storageAccountId": "[parameters('storageAccountId')]",
        "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
        "eventHubName": "[parameters('eventHubName')]",
        "logs": [
          {
            "category": "AutoscaleScaleActions",
            "enabled": true
          },
          {
            "category": "AutoscaleEvaluations",
            "enabled": true
          }
        ]
      }
    }
  ]
}
```

## <a name="activity-log"></a>활동 로그
Azure 활동 로그의 경우 `Microsoft.Insights/diagnosticSettings`유형의 리소스를 추가 합니다. 사용 가능한 범주는 [활동 로그의 범주](activity-log-view.md#categories-in-the-activity-log)에 나열 됩니다. 다음은 Log Analytics 작업 영역, 저장소 계정 및 이벤트 허브에 대 한 모든 활동 로그 범주를 수집 하는 템플릿입니다.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceID of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
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
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "location": "global",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```


## <a name="next-steps"></a>다음 단계
* 자세한 내용은 [Azure의 플랫폼 로그를](platform-logs-overview.md)참조 하세요.
* [진단 설정](diagnostic-settings.md)에 대해 알아봅니다.
