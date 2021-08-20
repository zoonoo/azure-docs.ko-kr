---
title: 데이터 수집 규칙에 대한 Resource Manager 템플릿 샘플
description: Azure Monitor에서 데이터 수집 규칙과 가상 머신 간의 연결을 만들기 위한 샘플 Azure Resource Manager 템플릿.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 24bc9b0f92a82354f511f534eb9b0e86d59d336b
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112201269"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>Azure Monitor의 데이터 수집 규칙에 대한 Resource Manager 템플릿 샘플
이 문서에는 [데이터 수집 규칙](data-collection-rule-overview.md)과 [Azure Monitor 에이전트](./azure-monitor-agent-overview.md) 간의 연결을 만들기 위한 샘플 [Azure Resource Manager 템플릿](../../azure-resource-manager/templates/syntax.md)이 포함되어 있습니다. 각 샘플에는 템플릿 파일 및 템플릿에 제공할 샘플 값이 포함된 매개 변수 파일이 포함되어 있습니다.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="create-rule-sample"></a>규칙 만들기(샘플)

[템플릿 형식](/azure/templates/microsoft.insights/datacollectionrules) 보기

## <a name="create-association-with-azure-vm"></a>Azure VM을 사용하여 연결 만들기

다음 샘플에서는 Azure 가상 머신과 데이터 수집 규칙 간에 연결을 만듭니다.

### <a name="template-file"></a>템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this virtual machine.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
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
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="create-association-with-azure-arc"></a>Azure Arc를 사용하여 연결 만들기

다음 샘플에서는 Azure Arc 사용 서버와 데이터 수집 규칙 간에 연결을 만듭니다.

### <a name="template-file"></a>템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.HybridCompute/machines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this Arc server.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
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
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```


## <a name="next-steps"></a>다음 단계

* [Azure Monitor에 대한 다른 샘플 템플릿을 가져옵니다](../resource-manager-samples.md).
* [Log Analytics 에이전트에 대해 자세히 알아보세요](./log-analytics-agent.md).
* [진단 확장에 대해 자세히 알아보세요](./diagnostics-extension-overview.md).
