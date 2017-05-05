---
title: "Resource Manager 템플릿을 사용하여 활동 로그 경고 만들기 | Microsoft Docs"
description: "Azure 리소스가 만들어질 때 알림을 받습니다."
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 239b8fe2a7724bb34e7060c90af73825e61d8398
ms.lasthandoff: 04/12/2017


---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 활동 로그 경고 만들기
이 문서에서는 [Azure Resource Manager 템플릿](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates)을 사용하여 활동 로그 경고를 구성하는 방법을 설명합니다. 이를 통해 만든 리소스에 대한 경고를 자동 배포 프로세스의 일부로 자동으로 설정할 수 있습니다.

기본적인 단계는 다음과 같습니다.

1.    활동 로그 경고를 만드는 방법을 설명하는 JSON 파일로 템플릿을 만듭니다.
2.    [배포 방법을 사용하여 템플릿을 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)합니다.

아래에서는 먼저 활동 로그 경고만을 위한 Resource Manager 템플릿을 만들고 이후 다른 리소스를 만드는 동안 활동 로그 경고에 대한 템플릿을 만드는 방법을 설명합니다.

## <a name="resource-manager-template-for-an-activity-log-alert"></a>활동 로그 경고에 대한 Resource Manager 템플릿
Resource Manager 템플릿을 사용하여 활동 로그 경고를 만들려면 `microsoft.insights/activityLogAlerts` 형식의 리소스를 만들어 관련 속성을 기입합니다. 다음은 활동 로그 경고을 만드는 템플릿입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-03-01-preview",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ] 
        },
        "actions": {
          "actionGroups": 
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계
[경고](monitoring-overview-alerts.md)에 대해 자세히 알아보기  
[Resource Manager 템플릿을 사용하여 작업 그룹](monitoring-create-action-group-with-resource-manager-template.md)을 추가하는 방법

