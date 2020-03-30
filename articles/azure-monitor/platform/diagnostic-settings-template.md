---
title: Resource Manager 템플릿을 사용하여 Azure에서 진단 설정 만들기
description: 리소스 관리자 템플릿을 사용하여 진단 설정을 만들어 Azure 플랫폼 로그를 Azure 모니터 로그, Azure 저장소 또는 Azure 이벤트 허브로 전달합니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: a2569ca3f998030680bd7dbd872d71ccd372a25d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672432"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용하여 Azure에서 진단 설정 만들기
Azure Monitor의 [진단 설정은](diagnostic-settings.md) Azure 리소스 및 해당 리소스가 종속된 Azure 플랫폼에서 수집되는 [플랫폼 로그를](platform-logs-overview.md) 보낼 위치를 지정합니다. 이 문서에서는 [Azure Resource Manager 템플릿을](../../azure-resource-manager/templates/template-syntax.md) 사용하여 다른 대상에 대한 플랫폼 로그를 수집하도록 진단 설정을 만들고 구성하는 데 대한 세부 정보와 예제를 제공합니다.

> [!NOTE]
> 다른 Azure 리소스에 대한 진단 설정과 같은 PowerShell 또는 CLI를 사용하여 Azure 활동 로그에 대한 진단 [설정을 만들](diagnostic-settings.md) 수 없으므로 이 문서의 정보를 사용하여 활동 로그에 대한 리소스 관리자 템플릿을 만들고 PowerShell 또는 CLI를 사용하여 템플릿을 배포합니다.

## <a name="deployment-methods"></a>배포 방법
PowerShell 및 CLI를 포함한 유효한 방법을 사용하여 리소스 관리자 템플릿을 배포할 수 있습니다. 활동 로그에 대한 진단 설정은 `az deployment create` CLI `New-AzDeployment` 또는 PowerShell용을 사용하는 구독에 배포해야 합니다. 리소스 로그에 대한 진단 설정은 CLI 또는 `az group deployment create` `New-AzResourceGroupDeployment` PowerShell용을 사용하는 리소스 그룹에 배포해야 합니다.

자세한 내용은 [리소스 관리자 템플릿 및 Azure PowerShell을 사용 하 고](../../azure-resource-manager/templates/deploy-powershell.md) 리소스 관리자 템플릿 및 Azure [CLI를 사용 하 고 리소스](../../azure-resource-manager/templates/deploy-cli.md) 배포 리소스를 사용 하 고 리소스를 배포 를 참조 하십시오. 





## <a name="resource-logs"></a>리소스 로그
리소스 로그의 경우 템플릿에 `<resource namespace>/providers/diagnosticSettings` 형식의 리소스를 추가합니다. 속성 섹션은 [진단 설정 - 만들기 또는 업데이트에](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate)설명된 형식을 따릅니다. 수집하려는 `category` 리소스에 유효한 각 범주에 대한 `logs` 섹션을 제공합니다. 리소스가 `metrics` 메트릭을 지원하는 경우 속성을 추가하여 동일한 대상에 리소스 [메트릭을 수집합니다.](metrics-supported.md)

다음은 Log Analytics 작업 영역, 저장소 계정 및 이벤트 허브에 대한 특정 리소스에 대한 리소스 로그 범주를 수집하는 템플릿입니다.

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



### <a name="example"></a>예제
다음은 리소스 로그를 이벤트 허브, 저장소 계정 및 Log Analytics 작업 영역으로 스트리밍할 수 있는 자동 크기 조정 설정에 대한 진단 설정을 만드는 예제입니다.

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
Azure 활동 로그의 경우 형식의 `Microsoft.Insights/diagnosticSettings`리소스를 추가합니다. 사용 가능한 범주는 [활동 로그의 범주에](activity-log-view.md#categories-in-the-activity-log)나열됩니다. 다음은 모든 활동 로그 범주를 Log Analytics 작업 영역, 저장소 계정 및 이벤트 허브로 수집하는 템플릿입니다.


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
* [Azure 에서 플랫폼 로그에](platform-logs-overview.md)대해 자세히 알아보기
* 진단 [설정에](diagnostic-settings.md)대해 자세히 알아봅니다.
