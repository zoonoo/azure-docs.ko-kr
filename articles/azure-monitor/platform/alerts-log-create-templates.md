---
title: Azure Resource Manager 템플릿으로 로그 경고 만들기 | Microsoft Docs
description: 리소스 관리자 템플릿을 사용 하 여 로그 경고를 만드는 방법에 대해 알아봅니다.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 08/26/2020
ms.subservice: alerts
ms.openlocfilehash: b23f1d455610222ffa5713773a0bfb947f23ce34
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343774"
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 로그 경고 만들기

로그 경고를 통해 사용자는 [Log Analytics](../log-query/get-started-portal.md) 쿼리를 사용 하 여 설정 된 빈도 마다 리소스 로그를 평가 하 고 결과에 따라 경고를 발생 시킬 수 있습니다. 규칙은 [작업 그룹](./action-groups.md)을 사용 하 여 하나 이상의 작업 실행을 트리거할 수 있습니다. [로그 경고의 기능 및 용어에 대해 자세히 알아보세요](alerts-unified-log.md).

이 문서에서는 [Azure Resource Manager 템플릿을](../../azure-resource-manager/templates/template-syntax.md) 사용 하 여 Azure Monitor에서 [로그 경고](alerts-unified-log.md) 를 구성 하는 방법을 보여 줍니다. Resource Manager 템플릿을 사용하면 환경 전체에서 일관되고 재현 가능한 방법으로 경보를 프로그래밍 방식으로 설정할 수 있습니다. 로그 경고는 리소스 공급자에 생성 됩니다 `Microsoft.Insights/scheduledQueryRules` . [예약 된 쿼리 규칙 api](/rest/api/monitor/scheduledqueryrules/)에 대 한 api 참조를 참조 하세요.

기본적인 단계는 다음과 같습니다.

1. 다음 템플릿 중 하나를 경고 생성 방법을 설명하는 JSON 파일로 사용합니다.
2. 해당 매개 변수 파일을 편집 하 고 JSON으로 사용 하 여 경고를 사용자 지정 합니다.
4. 배포 방법을 사용하여 템플릿을 배포합니다.

> [!NOTE]
> [Log Analytics 작업 영역의](../log-query/get-started-portal.md) 로그 데이터를 Azure Monitor 메트릭 저장소로 보낼 수 있습니다. 메트릭 경고는 작업 하는 데이터에 따라 더 적합할 수 있는 [동작이 다릅니다](alerts-metric-overview.md). 로그를 메트릭에 라우팅하는 방법 및 방법에 대 한 자세한 내용은 [로그에 대 한 메트릭 경고](alerts-metric-logs.md)를 참조 하세요.

> [!NOTE]
> 레거시 [Log Analytics 경고 API](api-alerts.md) 를 사용 하 여 관리 하는 데 사용 되는 Log Analytics에 대 한 로그 경고 및 [저장 된 검색 및 경고 Log Analytics](../insights/solutions.md)의 레거시 템플릿을 사용 합니다. [현재 SCHEDULEDQUERYRULES API로 전환 하는 방법에 대해 자세히 알아보세요](alerts-log-api-switch.md).


## <a name="simple-template-up-to-api-version-2018-04-16"></a>간단한 템플릿 (API 버전 2018-04-16까지)

[결과 로그 경고 수](alerts-unified-log.md#count-of-the-results-table-rows) 를 기반으로 하는 [예약 된 쿼리 규칙 생성](/rest/api/monitor/scheduledqueryrules/createorupdate) 템플릿 (샘플 데이터 집합 (변수)):

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertDescription": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"requests",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4"
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescription')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "aznsAction":{
                    "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]"
                }
            }
        }
    } ]
}

```

이 JSON은 [Azure Portal Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)을 사용 하 여 저장 및 배포할 수 있습니다.

## <a name="template-with-cross-resource-query-up-to-api-version-2018-04-16"></a>리소스 간 쿼리가 포함 된 템플릿 (최대 API 버전 2018-04-16)

[리소스 간](../log-query/cross-workspace-query.md) (샘플 데이터 집합)을 쿼리 하는 [메트릭 측정](./alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) 을 기반으로 하는 [예약 된 쿼리 규칙 생성](/rest/api/monitor/scheduledqueryrules/createorupdate) 템플릿:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
        "alertName": "sample log alert",
        "alertDescr": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"union workspace(\"servicews\").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
            "Resource1": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Resource2": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceapp",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4",
            "SuppressTimeinMin": 20
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "metricMeasurement": {
            "thresholdOperator": "Equal",
            "threshold": "1",
            "metricTriggerType": "Consecutive",
            "metricColumn": "Classification"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescr')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "authorizedResources": "[concat(array(variables('alertSource').Resource1), array(variables('alertSource').Resource2))]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "throttlingInMin": "[variables('alertActions').SuppressTimeinMin]",
                "aznsAction":{
                    "actionGroup": "[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]",
                    "metricTrigger":{
                        "thresholdOperator": "[variables('metricMeasurement').thresholdOperator]",
                        "threshold": "[variables('metricMeasurement').threshold]",
                        "metricColumn": "[variables('metricMeasurement').metricColumn]",
                        "metricTriggerType": "[variables('metricMeasurement').metricTriggerType]"
                    }
                }
            }
        }
    } ]
}
```

> [!IMPORTANT]
> 로그 경고에 리소스 간 쿼리를 사용하는 경우 [authorizedResources](/rest/api/monitor/scheduledqueryrules/createorupdate#source) 사용은 반드시 필요하며 사용자는 시작된 리소스의 목록에 액세스할 수 있어야 합니다.

이 JSON은 [Azure Portal Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)을 사용 하 여 저장 및 배포할 수 있습니다.

## <a name="template-for-all-resource-types-from-api-version-2020-05-01-preview"></a>모든 리소스 종류에 대 한 템플릿 (API 버전 2020-05-01-미리 보기)

모든 리소스 유형에 대 한 [예약 된 쿼리 규칙 생성](/rest/api/monitor/scheduledqueryrules/createorupdate) 템플릿 (샘플 데이터 집합을 변수로 설정):

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
        "location": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Location of the alert"
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
        "query": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "metricMeasureColumn": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the measure column used in the alert evaluation."
            }
        },
        "resourceIdColumn": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the resource ID column used in the alert targeting the alerts."
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
        "muteActionsDuration": {
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
                "description": "Mute actions for the chosen period of time (in ISO 8601 duration format) after the alert is fired."
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
            "type": "Microsoft.Insights/scheduledQueryRules",
            "location": "[parameters('location')]",
            "apiVersion": "2020-05-01-preview",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "allOf": [
                        {
                            "query": "[parameters('query')]",
                            "metricMeasureColumn": "[parameters('metricMeasureColumn')]",
                            "resourceIdColumn": "[parameters('resourceIdColumn')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            }
                        }
                    ]
                },
                "muteActionsDuration": "[parameters('muteActionsDuration')]",
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

이 JSON은 [Azure Portal Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)을 사용 하 여 저장 및 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [로그 경고](./alerts-unified-log.md) 에 대 한 자세한 정보
* [로그 경고 관리](./alerts-log.md) 에 대 한 자세한 정보
* [로그 경고에 대 한 webhook 작업](./alerts-log-webhook.md) 이해
* [로그 쿼리에](../log-query/log-query-overview.md)대해 자세히 알아보세요.
