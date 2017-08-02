---
title: "Resource Manager 템플릿을 사용하여 작업 그룹 만들기 | Microsoft Docs"
description: "작업 그룹을 사용하여 특정 이벤트가 발생할 때 전자 메일, SMS 또는 웹후크 호출로 알릴 수 있습니다."
author: anirudhcavale
manager: orenr
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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 88e7b2e7781b80ea360531f4c3a45256de83b594
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="create-an-action-group-with-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 작업 그룹 만들기
이 문서에서는 [Azure Resource Manager 템플릿](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)을 사용하여 작업 그룹을 구성하는 방법을 설명합니다. 템플릿을 사용하여 작업 그룹이 만들어질 때 리소스에서 작업 그룹이 자동으로 설정되도록 함으로써 알림이 트리거될 때 올바른 모든 당사자에게 알림이 제공되도록 할 수 있습니다.

기본적인 단계는 다음과 같습니다.

1.  작업 그룹을 만드는 방법을 설명하는 JSON 파일로 템플릿을 만듭니다.
2.  [배포 방법을 사용하여 템플릿을 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)합니다.

아래에서는 작업 정의가 템플릿에 하드 코딩되는 작업 그룹에 대해 우선 Resource Manager 템플릿을 만든 다음, 템플릿을 배포할 때 웹후크 구성 정보를 입력 매개 변수로 사용하는 템플릿에 대해 Resource Manager 템플릿을 만드는 방법을 설명합니다.

## <a name="resource-manager-template-for-an-action-group"></a>작업 그룹에 대한 Resource Manager 템플릿

Resource Manager 템플릿을 사용하여 작업 그룹을 만들려면 `Microsoft.Insights/actionGroups` 형식의 리소스를 만들어 관련 속성을 기입합니다. 다음은 작업 그룹을 만드는 몇 가지 예제 템플릿입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2017-04-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2017-04-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>다음 단계
[작업 그룹](monitoring-action-groups.md)에 대해 자세히 알아보기  
[경고](monitoring-overview-alerts.md)에 대해 자세히 알아보기  
[Resource Manager 템플릿을 사용하여 경고](monitoring-create-activity-log-alerts-with-resource-manager-template.md)를 추가하는 방법

