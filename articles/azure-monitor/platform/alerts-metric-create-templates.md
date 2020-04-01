---
title: Resource Manager 템플릿을 사용하여 메트릭 경고 만들기
description: Resource Manager 템플릿을 사용하여 메트릭 경고를 만드는 방법에 대해 알아봅니다.
author: harelbr
ms.author: harelbr
services: azure-monitor
ms.topic: conceptual
ms.date: 2/24/2020
ms.subservice: alerts
ms.openlocfilehash: fe1f1d2700619bfcac4512bb0e631262edf8401c
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398090"
---
# <a name="create-a-metric-alert-with-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 메트릭 경고 만들기

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

이 문서에서는 [Azure Resource Manager 템플릿](../../azure-resource-manager/templates/template-syntax.md)을 사용하여 Azure Monitor에서 [최신 메트릭 경고](../../azure-monitor/platform/alerts-metric-near-real-time.md)를 구성하는 방법을 설명합니다. Resource Manager 템플릿을 사용하면 환경 전체에서 일관되고 재현 가능한 방법으로 경보를 프로그래밍 방식으로 설정할 수 있습니다. 최신 메트릭 경고는 현재 [이 리소스 유형 집합](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)에 대해 사용할 수 있습니다.

> [!IMPORTANT]
> 리소스 유형에 대한 메트릭 경고를 만들기 위한 리소스 템플릿: Azure Log `Microsoft.OperationalInsights/workspaces`Analytics 작업 영역(예: ) - 추가 단계가 필요합니다. 자세한 내용은 [로그에 대한 메트릭 경고 - 리소스 템플릿](../../azure-monitor/platform/alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs)의 문서를 참조하세요.

기본적인 단계는 다음과 같습니다.

1. 다음 템플릿 중 하나를 경고 생성 방법을 설명하는 JSON 파일로 사용합니다.
2. 해당 매개 변수 파일을 JSON으로 편집하고 사용하여 경고를 사용자 지정합니다.
3. 매개 `metricName` 변수에 대 한 [Azure 모니터 지원 되는 메트릭에서](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)사용 가능한 메트릭을 참조 합니다.
4. [배포 방법](../../azure-resource-manager/templates/deploy-powershell.md)을 사용하여 템플릿을 배포합니다.

## <a name="template-for-a-simple-static-threshold-metric-alert"></a>간단한 정적 임계값 메트릭 경고에 대한 템플릿

Resource Manager 템플릿을 사용하여 경고를 만들려면 `Microsoft.Insights/metricAlerts` 형식의 리소스를 만들어 관련 속성을 기입합니다. 다음은 메트릭 경고 규칙을 만드는 샘플 템플릿입니다.

이 연습의 목적을 위해 아래의 json을 simplestaticmetricalert.json으로 저장합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

경고 규칙에 대한 스키마 및 속성 설명은 [여기에서 제공합니다](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate).

명령줄 또는 매개 변수 파일을 통해 매개 변수의 값을 설정할 수 있습니다. 샘플 매개 변수 파일이 아래에 제공됩니다.

아래의 json을 simplestaticmetricalert.parameters.json으로 저장하고 필요에 따라 수정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Metric Alert"
        },
        "alertDescription": {
            "value": "New metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/Microsoft.Compute/virtualMachines/replace-with-resource-name"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "80"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group"
        }
    }
}
```


PowerShell 또는 Azure CLI를 사용한 템플릿 및 매개 변수 파일을 사용하여 메트릭 경고를 만들 수 있습니다.

Azure PowerShell 사용

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile simplestaticmetricalert.json -TemplateParameterFile simplestaticmetricalert.parameters.json
```


Azure CLI 사용
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file simplestaticmetricalert.json \
    --parameters @simplestaticmetricalert.parameters.json
```

> [!NOTE]
>
> 메트릭 경고는 다른 리소스 그룹에서 대상 리소스로 만들 수 있지만, 대상 리소스와 동일한 리소스 그룹을 사용하는 것이 좋습니다.

## <a name="template-for-a-simple-dynamic-thresholds-metric-alert"></a>간단한 동적 임계값 메트릭 경고에 대한 템플릿

Resource Manager 템플릿을 사용하여 경고를 만들려면 `Microsoft.Insights/metricAlerts` 형식의 리소스를 만들어 관련 속성을 기입합니다. 다음은 메트릭 경고 규칙을 만드는 샘플 템플릿입니다.

이 연습의 목적을 위해 아래의 json을 simpledynamicmetricalert.json으로 저장합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
    "ignoreDataBefore": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Use this option to set the date from which to start learning the metric historical data and calculate the dynamic thresholds (in ISO8601 format, e.g. '2019-12-31T22:00:00Z')."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one hour. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                "ignoreDataBefore": "[parameters('ignoreDataBefore')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

경고 규칙에 대한 스키마 및 속성 설명은 [여기에서 제공합니다](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate).

명령줄 또는 매개 변수 파일을 통해 매개 변수의 값을 설정할 수 있습니다. 샘플 매개 변수 파일이 아래에 제공됩니다. 

아래의 json을 simpledynamicmetricalert.parameters.json으로 저장하고 필요에 따라 수정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Metric Alert with Dynamic Thresholds"
        },
        "alertDescription": {
            "value": "New metric alert with Dynamic Thresholds created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/Microsoft.Compute/virtualMachines/replace-with-resource-name"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterOrLessThan"
        },
        "alertSensitivity": {
            "value": "Medium"
        },
        "numberOfEvaluationPeriods": {
            "value": "4"
        },
        "minFailingPeriodsToAlert": {
            "value": "3"
        },
    "ignoreDataBefore": {
            "value": ""
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group"
        }
    }
}
```


PowerShell 또는 Azure CLI를 사용한 템플릿 및 매개 변수 파일을 사용하여 메트릭 경고를 만들 수 있습니다.

Azure PowerShell 사용

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile simpledynamicmetricalert.json -TemplateParameterFile simpledynamicmetricalert.parameters.json
```


Azure CLI 사용
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file simpledynamicmetricalert.json \
    --parameters @simpledynamicmetricalert.parameters.json
```

> [!NOTE]
>
> 메트릭 경고는 다른 리소스 그룹에서 대상 리소스로 만들 수 있지만, 대상 리소스와 동일한 리소스 그룹을 사용하는 것이 좋습니다.

## <a name="template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria"></a>여러 기준을 모니터링하는 정적 임계값 메트릭 경고용 템플릿

최신 메트릭 경고는 다차원 메트릭에 대한 경고를 지원하고 여러 조건을 지원합니다. 다음 템플릿을 사용하여 차원 메트릭에 대한 고급 메트릭 경고 규칙을 만들고 여러 기준을 지정할 수 있습니다.

여러 기준이 포함된 경고 규칙에서 차원을 사용할 때 다음 제약 조건을 확인하십시오.
- 각 기준 내에서 치수당 하나의 값만 선택할 수 있습니다.
- ""\*"를 차원 값으로 사용할 수 없습니다.
- 서로 다른 기준에 구성된 메트릭이 동일한 차원을 지원하는 경우 구성된 차원 값은 해당 모든 메트릭(관련 기준)에 대해 동일한 방식으로 명시적으로 설정되어야 합니다.
    - 아래 예제에서는 **트랜잭션** 및 **SuccessE2ELatency** 메트릭 모두 **ApiName** 차원을 가지며 *기준1은* **ApiName** 차원에 대한 *"GetBlob"* 값을 지정하므로 *기준2는* **ApiName** 차원에 대해 *"GetBlob"* 값도 설정해야 합니다.


이 연습의 목적을 위해 아래의 json을 advancedstaticmetricalert.json으로 저장합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the resource emitting the metric that will be used for the comparison."
            }
        },
        "criterion1":{
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "criterion2": {
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": { 
        "criterion1": "[array(parameters('criterion1'))]",
        "criterion2": "[array(parameters('criterion2'))]",
        "criteria": "[concat(variables('criterion1'),variables('criterion2'))]"
     },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": "[variables('criteria')]"
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

위의 템플릿을 아래 제공된 매개 변수 파일과 함께 사용할 수 있습니다. 

이 연습의 목적을 위해 아래의 json을 advancedstaticmetricalert.parameters.json으로 저장하고 수정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Multi-dimensional Metric Alert (Replace with your alert name)"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template (Replace with your alert description)"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourcegroup-name/providers/Microsoft.Storage/storageAccounts/replace-with-storage-account"
        },
        "criterion1": {
            "value": {
                    "name": "1st criterion",
                    "metricName": "Transactions",
                    "dimensions": [
                        {
                            "name":"ResponseType",
                            "operator": "Include",
                            "values": ["Success"]
                        },
                        {
                            "name":"ApiName",
                            "operator": "Include",
                            "values": ["GetBlob"]
                        }
                    ],
                    "operator": "GreaterThan",
                    "threshold": "5",
                    "timeAggregation": "Total"
                }
        },
        "criterion2": {
            "value":{
                "name": "2nd criterion",
                "metricName": "SuccessE2ELatency",
                "dimensions": [
                    {
                        "name":"ApiName",
                        "operator": "Include",
                        "values": ["GetBlob"]
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "250",
                "timeAggregation": "Average"
            }
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-actiongroup-name"
        }
    }
}
```


현재 작업 디렉터리의 PowerShell 또는 Azure CLI를 사용한 템플릿 및 매개 변수 파일을 사용하여 메트릭 경고를 만들 수 있습니다.

Azure PowerShell 사용
```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile advancedstaticmetricalert.json -TemplateParameterFile advancedstaticmetricalert.parameters.json
```



Azure CLI 사용
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file advancedstaticmetricalert.json \
    --parameters @advancedstaticmetricalert.parameters.json
```


## <a name="template-for-a-static-metric-alert-that-monitors-multiple-dimensions"></a>여러 차원을 모니터링하는 정적 메트릭 경고용 템플릿

다음 템플릿을 사용하여 차원 메트릭에 정적 메트릭 경고 규칙을 만들 수 있습니다.

단일 경고 규칙은 한 번에 여러 메트릭 타임계를 모니터링할 수 있으며, 이로 인해 관리할 경고 규칙이 줄어듭니다.

아래 예제에서 경고 규칙은 **트랜잭션** 메트릭에 대한 **ResponseType** 및 **ApiName** 차원의 차원 값 조합을 모니터링합니다.
1. **ResponsType** - "와일드카드"의\*사용은 미래 값을 포함하여 **ResponseType** 차원의 각 값에 대해 다른 시간계가 개별적으로 모니터링된다는 것을 의미합니다.
2. **ApiName** - **GetBlob** 및 **PutBlob** 차원 값에 대해서만 다른 시간계가 모니터링됩니다.

예를 들어 이 경고 규칙에 의해 모니터링되는 몇 가지 잠재적인 서계는 다음과 같습니다.
- 메트릭 = *트랜잭션,* 응답 유형 = *성공*, ApiName = *GetBlob*
- 메트릭 = *트랜잭션,* 응답 유형 = *성공*, ApiName = *PutBlob*
- 메트릭 = *트랜잭션,* 응답 유형 = *서버 시간 시간,* ApiName = *GetBlob*
- 메트릭 = *트랜잭션,* 응답 유형 = *서버 시간 시간,* ApiName = *PutBlob*

이 연습의 목적을 위해 아래의 json을 다차원정적metricalert.json으로 저장합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the resource emitting the metric that will be used for the comparison."
            }
        },
        "criterion":{
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": { 
        "criteria": "[array(parameters('criterion'))]"
     },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": "[variables('criteria')]"
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

위의 템플릿을 아래 제공된 매개 변수 파일과 함께 사용할 수 있습니다. 

이 연습의 목적을 위해 아래 json을 다차원staticmetricalert.parameters.json으로 저장하고 수정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New multi-dimensional metric alert rule (replace with your alert name)"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert rule created via template (replace with your alert description)"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourcegroup-name/providers/Microsoft.Storage/storageAccounts/replace-with-storage-account"
        },
        "criterion": {
            "value": {
                    "name": "Criterion",
                    "metricName": "Transactions",
                    "dimensions": [
                        {
                            "name":"ResponseType",
                            "operator": "Include",
                            "values": ["*"]
                        },
                        {
                "name":"ApiName",
                            "operator": "Include",
                            "values": ["GetBlob", "PutBlob"]    
                        }
                    ],
                    "operator": "GreaterThan",
                    "threshold": "5",
                    "timeAggregation": "Total"
                }
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-actiongroup-name"
        }
    }
}
```


현재 작업 디렉터리의 PowerShell 또는 Azure CLI를 사용한 템플릿 및 매개 변수 파일을 사용하여 메트릭 경고를 만들 수 있습니다.

Azure PowerShell 사용
```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile multidimensionalstaticmetricalert.json -TemplateParameterFile multidimensionalstaticmetricalert.parameters.json
```



Azure CLI 사용
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file multidimensionalstaticmetricalert.json \
    --parameters @multidimensionalstaticmetricalert.parameters.json
```


## <a name="template-for-a-dynamic-thresholds-metric-alert-that-monitors-multiple-dimensions"></a>여러 차원을 모니터링하는 동적 임계값 메트릭 경고용 템플릿

다음 템플릿을 사용하여 차원 메트릭에 대한 고급 동적 임계값 메트릭 경고 규칙을 만들 수 있습니다.

단일 동적 임계값 경고 규칙은 한 번에 수백 개의 메트릭 서계열(심지어 다른 유형)에 대한 맞춤형 임계값을 만들 수 있으며, 이로 인해 관리할 경고 규칙이 줄어듭니다.

아래 예제에서 경고 규칙은 **트랜잭션** 메트릭에 대한 **ResponseType** 및 **ApiName** 차원의 차원 값 조합을 모니터링합니다.
1. **ResponsType** - 미래 값을 포함하여 **ResponseType** 차원의 각 값에 대해 다른 열이 개별적으로 모니터링됩니다.
2. **ApiName** - **GetBlob** 및 **PutBlob** 차원 값에 대해서만 다른 시간계가 모니터링됩니다.

예를 들어 이 경고 규칙에 의해 모니터링되는 몇 가지 잠재적인 서계는 다음과 같습니다.
- 메트릭 = *트랜잭션,* 응답 유형 = *성공*, ApiName = *GetBlob*
- 메트릭 = *트랜잭션,* 응답 유형 = *성공*, ApiName = *PutBlob*
- 메트릭 = *트랜잭션,* 응답 유형 = *서버 시간 시간,* ApiName = *GetBlob*
- 메트릭 = *트랜잭션,* 응답 유형 = *서버 시간 시간,* ApiName = *PutBlob*

이 연습의 목적을 위해 아래의 json을 advanceddynamicmetricalert.json으로 저장합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the resource emitting the metric that will be used for the comparison."
            }
        },
        "criterion":{
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one hour. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": { 
        "criteria": "[array(parameters('criterion'))]"
     },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": "[variables('criteria')]"
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

위의 템플릿을 아래 제공된 매개 변수 파일과 함께 사용할 수 있습니다. 

이 연습의 목적을 위해 아래의 json을 advanceddynamicmetricalert.parameters.json으로 저장하고 수정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Multi-dimensional Metric Alert with Dynamic Thresholds (Replace with your alert name)"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert with Dynamic Thresholds created via template (Replace with your alert description)"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourcegroup-name/providers/Microsoft.Storage/storageAccounts/replace-with-storage-account"
        },
        "criterion": {
            "value": {
                    "criterionType": "DynamicThresholdCriterion",
                    "name": "1st criterion",
                    "metricName": "Transactions",
                    "dimensions": [
                        {
                            "name":"ResponseType",
                            "operator": "Include",
                            "values": ["*"]
                        },
                        {
                            "name":"ApiName",
                            "operator": "Include",
                            "values": ["GetBlob", "PutBlob"]
                        }
                    ],
                    "operator": "GreaterOrLessThan",
                    "alertSensitivity": "Medium",
                    "failingPeriods": {
                        "numberOfEvaluationPeriods": "4",
                        "minFailingPeriodsToAlert": "3"
                    },
                    "timeAggregation": "Total"
                }
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-actiongroup-name"
        }
    }
}
```


현재 작업 디렉터리의 PowerShell 또는 Azure CLI를 사용한 템플릿 및 매개 변수 파일을 사용하여 메트릭 경고를 만들 수 있습니다.

Azure PowerShell 사용
```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile advanceddynamicmetricalert.json -TemplateParameterFile advanceddynamicmetricalert.parameters.json
```



Azure CLI 사용
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file advanceddynamicmetricalert.json \
    --parameters @advanceddynamicmetricalert.parameters.json
```

>[!NOTE]
>
> 동적 임계값을 사용하는 메트릭 경고 규칙에는 현재 여러 기준이 지원되지 않습니다.


## <a name="template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric"></a>사용자 지정 메트릭을 모니터링하는 정적 임계값 메트릭 경고용 템플릿

다음 템플릿을 사용하여 사용자 지정 메트릭에서 고급 정적 임계값 메트릭 경고 규칙을 만들 수 있습니다.

Azure 모니터의 사용자 지정 메트릭에 대해 자세히 알아보려면 [Azure 모니터의 사용자 지정 메트릭을](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-custom-overview)참조하십시오.

사용자 지정 메트릭에서 경고 규칙을 만들 때 메트릭 이름과 메트릭 네임스페이스를 모두 지정해야 합니다. 또한 아직 존재하지 않는 사용자 지정 측정항목에 경고 규칙을 만들 수 없으므로 맞춤 측정항목이 이미 보고되고 있는지 확인해야 합니다.

이 연습의 목적을 위해 아래의 json을 사용자 정의 staticmetricalert.json으로 저장합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "metricNamespace": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Namespace of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "How often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "metricNamespace": "[parameters('metricNamespace')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

위의 템플릿을 아래 제공된 매개 변수 파일과 함께 사용할 수 있습니다. 

이 연습의 목적을 위해 아래 의 json을 사용자 정의 staticmetricalert.parameters.json으로 저장하고 수정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New alert rule on a custom metric"
        },
        "alertDescription": {
            "value": "New alert rule on a custom metric created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/microsoft.insights/components/replace-with-application-insights-resource-name"
        },
        "metricName": {
            "value": "The custom metric name"
        },
        "metricNamespace": {
            "value": "Azure.ApplicationInsights"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "80"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group"
        }
    }
}
```


현재 작업 디렉터리의 PowerShell 또는 Azure CLI를 사용한 템플릿 및 매개 변수 파일을 사용하여 메트릭 경고를 만들 수 있습니다.

Azure PowerShell 사용
```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupOfTargetResource `
  -TemplateFile customstaticmetricalert.json -TemplateParameterFile customstaticmetricalert.parameters.json
```



Azure CLI 사용
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupOfTargetResource \
    --template-file customstaticmetricalert.json \
    --parameters @customstaticmetricalert.parameters.json
```

>[!NOTE]
>
> [Azure 포털을 통해 사용자 지정 메트릭을 탐색하여](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-custom-overview#browse-your-custom-metrics-via-the-azure-portal) 특정 사용자 지정 메트릭의 메트릭 네임스페이스를 찾을 수 있습니다.


## <a name="template-for-a-metric-alert-that-monitors-multiple-resources"></a>여러 리소스를 모니터링하는 메트릭 경고템플릿

이전 섹션에서는 단일 리소스를 모니터링하는 메트릭 경고를 만드는 Azure Resource Manager 템플릿 샘플에 대해 설명했습니다. 이제 Azure Monitor는 동일한 Azure 지역에 있는 리소스에 대해 단일 메트릭 경고 규칙을 통해 동일한 유형의 여러 리소스(동일한 유형)를 모니터링할 수 있도록 지원합니다. 이 기능은 현재 Azure 공용 클라우드에서만 지원되며 가상 컴퓨터, SQL 서버 데이터베이스, SQL 서버 탄력적 풀 및 Databox 에지 장치에만 지원됩니다. 또한 이 기능은 플랫폼 메트릭에만 사용할 수 있으며 사용자 지정 메트릭에는 지원되지 않습니다.

동적 임계값 경고 규칙은 수백 가지 메트릭 시리즈에 대한 맞춤형 임계값(심지어 다양한 형식의)을 한 번에 만드는 데 유용할 수 있습니다. 그러면 관리할 경고 규칙이 더 적어집니다.

이 섹션에서는 단일 규칙으로 여러 리소스를 모니터링하는 다음 세 가지 시나리오를 위한 Azure Resource Manager 템플릿을 설명합니다.

- 하나 이상의 리소스 그룹에 있는 모든 가상 머신(한 Azure 지역에 있는) 모니터링
- 구독의 모든 가상 시스템(하나의 Azure 리전에서)을 모니터링합니다.
- 구독의 가상 컴퓨터 목록(한 Azure 리전)을 모니터링합니다.

> [!NOTE]
>
> 여러 리소스를 모니터링하는 메트릭 경고 규칙에서는 하나의 조건만 허용됩니다.

### <a name="static-threshold-alert-on-all-virtual-machines-in-one-or-more-resource-groups"></a>하나 이상의 리소스 그룹에 있는 모든 가상 머신에 대한 정적 임계값 경고

이 템플릿은 하나 이상의 리소스 그룹에 있는 모든 가상 머신(한 Azure 지역에 있는)의 CPU 백분율을 모니터링하는 정적 임계값 메트릭 경고 규칙을 만듭니다.

이 연습의 목적을 위해 아래의 json을 all-vms-in-resource-group-static.json으로 저장합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetResourceGroup":{
            "type": "array",
            "minLength": 1,
            "metadata": {
                "description": "Full path of the resource group(s) where target resources to be monitored are in. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": "[parameters('targetResourceGroup')]",
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

위의 템플릿을 아래에 제공된 매개 변수 파일과 함께 사용할 수 있습니다.
이 연습의 목적을 위해 아래의 json을 all-vms-in-resource-group-static.json으로 저장하고 수정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource metric alert via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetResourceGroup":{
            "value": [
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name1",
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name2"
            ]
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "0"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

현재 작업 디렉터리의 PowerShell 또는 Azure CLI를 사용한 템플릿 및 매개 변수 파일을 사용하여 정적 메트릭 경고를 만들 수 있습니다.

Azure PowerShell 사용

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile all-vms-in-resource-group-static.json -TemplateParameterFile all-vms-in-resource-group-static.parameters.json
```

Azure CLI 사용

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file all-vms-in-resource-group-static.json \
    --parameters @all-vms-in-resource-group-static.parameters.json
```

### <a name="dynamic-thresholds-alert-on-all-virtual-machines-in-one-or-more-resource-groups"></a>하나 이상의 리소스 그룹에 있는 모든 가상 머신에 대한 동적 임계값 경고

이 템플릿은 하나 이상의 리소스 그룹에 있는 모든 가상 머신(한 Azure 지역에 있는)의 CPU 백분율을 모니터링하는 동적 임계값 메트릭 경고 규칙을 만듭니다.

이 연습의 목적을 위해 아래의 json을 all-vms-in-resource-group-dynamic.json으로 저장합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetResourceGroup":{
            "type": "array",
            "minLength": 1,
            "metadata": {
                "description": "Full path of the resource group(s) where target resources to be monitored are in. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one hour. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": "[parameters('targetResourceGroup')]",
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

위의 템플릿을 아래에 제공된 매개 변수 파일과 함께 사용할 수 있습니다.
이 연습의 목적을 위해 아래의 json을 all-vms-in-resource-group-dynamic.parameters.json으로 저장하고 수정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource metric alert with Dynamic Thresholds via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource metric alert with Dynamic Thresholds created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetResourceGroup":{
            "value": [
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name1",
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name2"
            ]
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterOrLessThan"
        },
        "alertSensitivity": {
            "value": "Medium"
        },
        "numberOfEvaluationPeriods": {
            "value": "4"
        },
        "minFailingPeriodsToAlert": {
            "value": "3"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

현재 작업 디렉터리의 PowerShell 또는 Azure CLI를 사용한 템플릿 및 매개 변수 파일을 사용하여 메트릭 경고를 만들 수 있습니다.

Azure PowerShell 사용

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile all-vms-in-resource-group-dynamic.json -TemplateParameterFile all-vms-in-resource-group-dynamic.parameters.json
```

Azure CLI 사용

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file all-vms-in-resource-group-dynamic.json \
    --parameters @all-vms-in-resource-group-dynamic.parameters.json
```

### <a name="static-threshold-alert-on-all-virtual-machines-in-a-subscription"></a>구독에 있는 모든 가상 머신에 대한 정적 임계값 경고

이 템플릿은 구독에 있는 모든 가상 머신(한 Azure 지역에 있는)의 CPU 백분율을 모니터링하는 정적 임계값 메트릭 경고 규칙을 만듭니다.

이 연습의 목적 위해 아래의 json을 all-vms-in-subscription-static.json으로 저장합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetSubscription":{
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Azure Resource Manager path up to subscription ID. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('targetSubscription')]"],
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

위의 템플릿을 아래에 제공된 매개 변수 파일과 함께 사용할 수 있습니다.
이 연습의 목적을 위해 아래의 json을 all-vms-in-subscription-static.parameters.json으로 저장하고 수정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource sub level metric alert via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource sub level metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetSubscription":{
            "value": "/subscriptions/replace-with-subscription-id"
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },        
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "0"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

현재 작업 디렉터리의 PowerShell 또는 Azure CLI를 사용한 템플릿 및 매개 변수 파일을 사용하여 메트릭 경고를 만들 수 있습니다.

Azure PowerShell 사용

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile all-vms-in-subscription-static.json -TemplateParameterFile all-vms-in-subscription-static.parameters.json
```

Azure CLI 사용

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file all-vms-in-subscription-static.json \
    --parameters @all-vms-in-subscription.parameters-static.json
```

### <a name="dynamic-thresholds-alert-on-all-virtual-machines-in-a-subscription"></a>구독에 있는 모든 가상 머신에 대한 동적 임계값 경고

이 템플릿은 구독에 있는 모든 가상 머신(한 Azure 지역에 있는)의 CPU 백분율을 모니터링하는 동적 임계값 메트릭 경고 규칙을 만듭니다.

이 연습의 목적을 위해 아래의 json을 all-vms-in-subscription-dynamic.json으로 저장합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetSubscription":{
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Azure Resource Manager path up to subscription ID. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one hour. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('targetSubscription')]"],
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

위의 템플릿을 아래에 제공된 매개 변수 파일과 함께 사용할 수 있습니다.
이 연습의 목적을 위해 아래의 json을 all-vms-in-subscription-dynamic.parameters.json으로 저장하고 수정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource sub level metric alert with Dynamic Thresholds via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource sub level metric alert with Dynamic Thresholds created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetSubscription":{
            "value": "/subscriptions/replace-with-subscription-id"
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterOrLessThan"
        },
        "alertSensitivity": {
            "value": "Medium"
        },
        "numberOfEvaluationPeriods": {
            "value": "4"
        },
        "minFailingPeriodsToAlert": {
            "value": "3"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

현재 작업 디렉터리의 PowerShell 또는 Azure CLI를 사용한 템플릿 및 매개 변수 파일을 사용하여 메트릭 경고를 만들 수 있습니다.

Azure PowerShell 사용

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile all-vms-in-subscription-dynamic.json -TemplateParameterFile all-vms-in-subscription-dynamic.parameters.json
```

Azure CLI 사용

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file all-vms-in-subscription-dynamic.json \
    --parameters @all-vms-in-subscription-dynamic.parameter-dynamics.json
```

### <a name="static-threshold-alert-on-a-list-of-virtual-machines"></a>가상 머신의 목록에서 정적 임계값 경고

이 템플릿은 구독에 있는 가상 머신 목록(한 Azure 지역에 있는)의 CPU 백분율을 모니터링하는 정적 임계값 메트릭 경고 규칙을 만듭니다.

이 연습의 목적을 위해 아래의 json을 list-of-vms-static.json으로 저장합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetResourceId":{
            "type": "array",
            "minLength": 1,
            "metadata": {
                "description": "array of Azure resource Ids. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroup/resource-group-name/Microsoft.compute/virtualMachines/vm-name"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": "[parameters('targetResourceId')]",
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

위의 템플릿을 아래에 제공된 매개 변수 파일과 함께 사용할 수 있습니다.
이 연습의 목적을 위해 아래의 json을 list-of-vms-static.parameters.json으로 저장하고 수정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource metric alert by list via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource metric alert by list created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetResourceId":{
            "value": [
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name1/Microsoft.Compute/virtualMachines/replace-with-vm-name1",
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name2/Microsoft.Compute/virtualMachines/replace-with-vm-name2"
            ]
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "0"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

현재 작업 디렉터리의 PowerShell 또는 Azure CLI를 사용한 템플릿 및 매개 변수 파일을 사용하여 메트릭 경고를 만들 수 있습니다.

Azure PowerShell 사용

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile list-of-vms-static.json -TemplateParameterFile list-of-vms-static.parameters.json
```

Azure CLI 사용

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file list-of-vms-static.json \
    --parameters @list-of-vms-static.parameters.json
```

### <a name="dynamic-thresholds-alert-on-a-list-of-virtual-machines"></a>가상 머신의 목록에 대한 동적 임계값 경고

이 템플릿은 구독에 있는 가상 머신 목록(한 Azure 지역에 있는)의 CPU 백분율을 모니터링하는 동적 임계값 메트릭 경고 규칙을 만듭니다.

이 연습의 목적을 위해 아래의 json을 list-of-vms-dynamic.json으로 저장합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetResourceId":{
            "type": "array",
            "minLength": 1,
            "metadata": {
                "description": "array of Azure resource Ids. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroup/resource-group-name/Microsoft.compute/virtualMachines/vm-name"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
             "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one hour. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT5M",
             "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": "[parameters('targetResourceId')]",
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

위의 템플릿을 아래에 제공된 매개 변수 파일과 함께 사용할 수 있습니다.
이 연습의 목적을 위해 아래의 json을 list-of-vms-dynamic.parameters.json으로 저장하고 수정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "Multi-resource metric alert with Dynamic Thresholds by list via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource metric alert with Dynamic Thresholds by list created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetResourceId":{
            "value": [
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name1/Microsoft.Compute/virtualMachines/replace-with-vm-name1",
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name2/Microsoft.Compute/virtualMachines/replace-with-vm-name2"
            ]
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterOrLessThan"
        },
        "alertSensitivity": {
            "value": "Medium"
        },
        "numberOfEvaluationPeriods": {
            "value": "4"
        },
        "minFailingPeriodsToAlert": {
            "value": "3"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

현재 작업 디렉터리의 PowerShell 또는 Azure CLI를 사용한 템플릿 및 매개 변수 파일을 사용하여 메트릭 경고를 만들 수 있습니다.

Azure PowerShell 사용

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name MultiResourceAlertDeployment -ResourceGroupName ResourceGroupWhereRuleShouldbeSaved `
  -TemplateFile list-of-vms-dynamic.json -TemplateParameterFile list-of-vms-dynamic.parameters.json
```

Azure CLI 사용

```azurecli
az login

az group deployment create \
    --name MultiResourceAlertDeployment \
    --resource-group ResourceGroupWhereRuleShouldbeSaved \
    --template-file list-of-vms-dynamic.json \
    --parameters @list-of-vms-dynamic.parameters.json
```

## <a name="template-for-an-availability-test-along-with-a-metric-alert"></a>메트릭 경고와 함께 가용성 테스트를 위한 템플릿

[애플리케이션 인사이트 가용성 테스트를](../../azure-monitor/app/monitor-web-app-availability.md) 통해 전 세계 여러 위치에서 웹 사이트/애플리케이션의 가용성을 모니터링할 수 있습니다. 가용성 테스트 경고는 가용성 테스트가 특정 수의 위치에서 실패할 때 알려줍니다.
메트릭 경고와 동일한 리소스 유형의 가용성 테스트 경고(Microsoft.Insights/metricAlerts). 다음 샘플 Azure 리소스 관리자 템플릿을 사용하여 간단한 가용성 테스트 및 관련 경고를 설정할 수 있습니다.

이 연습의 목적을 위해 아래의 json을 가용성 alert.json으로 저장하십시오.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appName": {
      "type": "string"
    },
    "pingURL": {
      "type": "string"
    },
    "pingText": {
      "type": "string",
      "defaultValue": ""
    },
    "actionGroupId": {
      "type": "string"
    },
    "location": {
      "type": "string"
    }
  },
  "variables": {
    "pingTestName": "[concat('PingTest-', toLower(parameters('appName')))]",
    "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
  },
  "resources": [
    {
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('location')]",
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 300,
        "Timeout": 120,
        "Kind": "ping",
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('pingURL'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExecutionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },
    {
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/metricAlerts",
      "apiVersion": "2018-03-01",
      "location": "global",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "description": "Alert for web test",
        "severity": 1,
        "enabled": true,
        "scopes": [
          "[resourceId('Microsoft.Insights/webtests',variables('pingTestName'))]",
          "[resourceId('Microsoft.Insights/components',parameters('appName'))]"
        ],
        "evaluationFrequency": "PT1M",
        "windowSize": "PT5M",
        "templateType": 0,
        "criteria": {
          "odata.type": "Microsoft.Azure.Monitor.WebtestLocationAvailabilityCriteria",
          "webTestId": "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]",
          "componentId": "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
          "failedLocationCount": 2
        },
        "actions": [
          {
            "actionGroupId": "[parameters('actionGroupId')]"
          }
        ]
      }
    }
  ]
}
```

명령줄 또는 매개 변수 파일을 통해 매개 변수의 값을 설정할 수 있습니다. 샘플 매개 변수 파일이 아래에 제공됩니다.


> [!NOTE]
>
> `&amp`; 은 & 대한 HTML 엔터티 참조입니다. URL 매개 변수는 여전히 단일 & 구분되지만 HTML에서 URL을 언급하는 경우 인코딩해야 합니다. 따라서 pingURL 매개 변수 값에 "&"가 있는 경우 ";"로`&amp`이스케이프해야 합니다.

아래 의 json을 가용성 alert.parameters.json으로 저장하고 필요에 따라 수정하십시오.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "appName": {
            "value": "Replace with your Application Insights resource name"
        },
        "pingURL": {
            "value": "https://www.yoursite.com"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/microsoft.insights/actiongroups/replace-with-action-group-name"
        },
        "location": {
            "value": "Replace with the location of your Application Insights resource"
        }
    }
}
```

PowerShell 또는 Azure CLI를 사용하여 템플릿 및 매개 변수 파일을 사용하여 가용성 테스트 및 관련 경고를 만들 수 있습니다.

Azure PowerShell 사용

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name AvailabilityAlertDeployment -ResourceGroupName ResourceGroupofApplicationInsightsComponent `
  -TemplateFile availabilityalert.json -TemplateParameterFile availabilityalert.parameters.json
```

Azure CLI 사용

```azurecli
az login

az group deployment create \
    --name AvailabilityAlertDeployment \
    --resource-group ResourceGroupofApplicationInsightsComponent \
    --template-file availabilityalert.json \
    --parameters @availabilityalert.parameters.json
```

## <a name="next-steps"></a>다음 단계

- [Azure의 경고](alerts-overview.md)에 대해 자세히 알아보기
- [Resource Manager 템플릿을 사용하여 작업 그룹을 만드는](action-groups-create-resource-manager-template.md) 방법에 대해 알아보기
- JSON 구문 및 속성의 경우 [Microsoft.Insights/metricAlerts](/azure/templates/microsoft.insights/metricalerts) 템플릿 참조를 참조하세요.
