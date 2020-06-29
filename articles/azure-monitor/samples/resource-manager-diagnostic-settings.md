---
title: 진단 설정에 대한 Resource Manager 템플릿 샘플
description: Azure Monitor 진단 설정을 Azure 리소스에 적용하기 위한 Azure Resource Manager 템플릿 샘플입니다.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 06/09/2020
ms.openlocfilehash: 0a71d50945d5be489872e90a5a6d9989295e1fa9
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84753523"
---
# <a name="resource-manager-template-samples-for-diagnostic-settings-in-azure-monitor"></a>Azure Monitor의 진단 설정에 대한 Resource Manager 템플릿 샘플
이 문서에는 Azure 리소스에 대한 진단 설정을 만들기 위한 [Azure Resource Manager 템플릿](../../azure-resource-manager/templates/template-syntax.md) 샘플이 포함되어 있습니다. 각 샘플에는 템플릿 파일 및 템플릿에 제공할 샘플 값이 포함된 매개 변수 파일이 포함되어 있습니다.

Azure 리소스에 대한 진단 설정을 만들려면 `<resource namespace>/providers/diagnosticSettings` 형식의 리소스를 템플릿에 추가합니다. 이 문서에서는 일부 리소스 유형에 대한 예제를 제공하지만 동일한 패턴을 다른 리소스 유형에 적용할 수 있습니다. 허용되는 로그 및 메트릭의 컬렉션은 각 리소스 유형에 따라 달라집니다.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="diagnostic-setting-for-activity-log"></a>활동 로그에 대한 진단 설정
다음 샘플에서는 `Microsoft.Insights/diagnosticSettings` 유형의 리소스를 템플릿에 추가하여 활동 로그에 대한 진단 설정을 만듭니다.

> [!IMPORTANT]
> 활동 로그에 대한 진단 설정은 Azure 리소스의 설정과 같은 리소스 그룹이 아닌 구독에 대해 생성됩니다. 리소스 관리 템플릿을 배포하려면 PowerShell에 대한 `New-AzSubscriptionDeployment` 또는 Azure CLI에 대한 `az deployment sub create`를 사용합니다.

### <a name="template-file"></a>템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }

    },
    "resources": [
        {
          "type": "Microsoft.KeyVault/vaults/providers/diagnosticSettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[(parameters('settingName')]",
          "dependsOn": [],
          "properties": {
            "workspaceId": "[parameters('workspaceId')]",
            "storageAccountId": "[parameters('storageAccountId')]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "logs": [
              {
                "category": "AuditEvent",
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
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```


## <a name="diagnostic-setting-for-azure-key-vault"></a>Azure Key Vault에 대한 진단 설정 
다음 샘플에서는 `Microsoft.KeyVault/vaults/providers/diagnosticSettings` 형식의 리소스를 템플릿에 추가하여 Azure Key Vault에 대한 진단 설정을 만듭니다.

### <a name="template-file"></a>템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "String"
        },
        "vaultName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }

    },
    "resources": [
        {
          "type": "Microsoft.KeyVault/vaults/providers/diagnosticSettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[concat(parameters('vaultName'), '/Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [],
          "properties": {
            "workspaceId": "[parameters('workspaceId')]",
            "storageAccountId": "[parameters('storageAccountId')]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "logs": [
              {
                "category": "AuditEvent",
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
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "vaultName": {
        "value": "MyVault"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-azure-sql-database"></a>Azure SQL 데이터베이스에 대한 진단 설정
다음 샘플에서는 `microsoft.sql/servers/databases/providers/diagnosticSettings` 형식의 리소스를 템플릿에 추가하여 Azure SQL 데이터베이스에 대한 진단 설정을 만듭니다.

### <a name="template-file"></a>템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "String"
        },        
        "serverName": {
            "type": "String"
        },
        "dbName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }

    },
    "resources": [
        {
          "type": "microsoft.sql/servers/databases/providers/diagnosticSettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[concat(parameters('serverName'),'/',parameters('dbName'),'/microsoft.insights', parameters('settingName'))]",
          "dependsOn": [],
          "properties": {
            "workspaceId": "[parameters('workspaceId')]",
            "storageAccountId": "[parameters('storageAccountId')]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "logs": [
              {
                "category": "SQLInsights",
                "enabled": true
              },
              {
                "category": "AutomaticTuning",
                "enabled": true
              },
              {
                "category": "QueryStoreRuntimeStatistics",
                "enabled": true
              },
              {
                "category": "QueryStoreWaitStatistics",
                "enabled": true
              },
              {
                "category": "Errors",
                "enabled": true
              },
              {
                "category": "DatabaseWaitStatistics",
                "enabled": true
              },
              {
                "category": "Timeouts",
                "enabled": true
              },
              {
                "category": "Blocks",
                "enabled": true
              },
              {
                "category": "Deadlocks",
                "enabled": true
              }
            ],
            "metrics": [
              {
                "category": "Basic",
                "enabled": true
              },
              {
                "category": "InstanceAndAppAdvanced",
                "enabled": true
              },
              {
                "category": "WorkloadManagement",
                "enabled": true
              }
            ]
          }
        }
    ]
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "serverName": {
        "value": "MySqlServer"
      },
      "dbName": {
        "value": "MySqlDb"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```


## <a name="next-steps"></a>다음 단계

* [Azure Monitor에 대한 다른 샘플 템플릿을 가져옵니다](resource-manager-samples.md).
* [진단 설정에 대해 자세히 알아보세요](../platform/diagnostic-settings.md).
