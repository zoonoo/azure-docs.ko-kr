---
title: "Azure Monitor에서 근 실시간 메트릭 경고 | Microsoft Docs"
description: "근 실시간 메트릭 경고를 사용하면 1분 간격으로 Azure 리소스 메트릭을 모니터링할 수 있습니다."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: d3e88a98e0ba93a630d131c25ca4dd5cb16f1b1a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>근 실시간 메트릭 경고(미리 보기)
이제 Azure Monitor는 근 실시간 메트릭 경고(미리 보기)라는 새로운 유형의 메트릭 경고를 지원합니다. 이 기능은 현재 공개 미리 보기로 제공됩니다.
이러한 경고는 몇 가지 점에서 일반 메트릭 경고와 다릅니다.

- **대기 시간 향상** - 근 실시간 메트릭 경고는 1분 간격으로 메트릭 값의 변경 사항을 모니터링할 수 있습니다.
- **메트릭 조건 세부 제어** - 근 실시간 메트릭 경고를 사용하면 사용자가 경고 규칙을 다양하게 정의할 수 있습니다. 이제 경고는 메트릭의 최대, 최소, 평균 및 전체 값을 모니터링하도록 지원합니다.
- **여러 가지 메트릭의 결합 모니터링** - 근 실시간 메트릭 경고는 단일 규칙을 가진 다중 메트릭(현재 두 개)을 모니터링할 수 있습니다. 두 메트릭이 지정된 기간 동안 해당 임계값을 위반하는 경우 경고가 트리거됩니다.
- **모듈식 알림 시스템** - 근 실시간 메트릭 경고는 [작업 그룹](monitoring-action-groups.md)을 사용합니다. 이 기능을 사용하면 모듈 방식으로 작업을 만들고 여러 경고 규칙에 다시 사용할 수 있습니다.

> [!NOTE]
> 근 실시간 메트릭 경고 기능은 현재 공개 미리 보기로 제공됩니다. 기능 및 사용자 환경을 변경할 수 있습니다.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>근 실시간 메트릭 경고을 만들 수 있는 리소스는 무엇인가요?
근 실시간 메트릭 경고에서 지원되는 리소스 종류의 전체 목록:

* Microsoft.ApiManagement/service
* Microsoft.Automation/automationAccounts
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Storage/storageAccounts
* Microsoft.Storage/storageAccounts/services
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.CognitiveServices/accounts

## <a name="near-real-time-metric-alerts-on-metrics-with-dimensions"></a>차원으로 메트릭의 근 실시간 메트릭 경고
근 실시간 메트릭 경고는 차원으로 메트릭의 경고를 지원합니다. 차원은 메트릭을 적절한 수준으로 필터링하는 방법입니다. 차원으로 메트릭의 근 실시간 메트릭 경고는 다음 리소스 종류에 대해 지원됩니다.

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts(US 지역의 저장소 계정에 대해서만 지원됨)
* Microsoft.Storage/storageAccounts/services(US 지역의 저장소 계정에 대해서만 지원됨)


## <a name="create-a-near-real-time-metric-alert"></a>근 실시간 메트릭 경고 만들기
현재 근 실시간 메트릭 경고는 Azure Portal을 통해서만 만들 수 있습니다. PowerShell, CLI(명령줄 인터페이스) 및 Azure Monitor REST API를 통해 근 실시간 메트릭 경고를 구성하는 지원이 곧 제공됩니다.

근 실시간 메트릭 경고에 대한 경고 만들기 환경은 새 **경고(미리 보기)** 환경으로 이동했습니다. 현재 경고 페이지가 **근 실시간 메트릭 경고 추가**를 표시하더라도 새 환경으로 리디렉션됩니다.

[여기](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)에 설명된 단계를 사용하여 근 실시간 메트릭 경고를 만들 수 있습니다.

## <a name="managing-near-real-time-metric-alerts"></a>근 실시간 메트릭 경고 관리
**근 실시간 메트릭 경고**를 만들면 [여기](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)에 설명된 단계를 사용하여 관리할 수 있습니다.

## <a name="payload-schema"></a>페이로드 스키마

POST 작업에는 모든 근 실시간 메트릭 경고에 대해 다음과 같은 JSON 페이로드와 스키마가 포함됩니다.

```json
{
    "WebhookName": "Alert1510875839452",
    "RequestBody": {
        "status": "Activated",
        "context": {
            "condition": {
                "metricName": "Percentage CPU",
                "metricUnit": "Percent",
                "metricValue": "17.7654545454545",
                "threshold": "1",
                "windowSize": "10",
                "timeAggregation": "Average",
                "operator": "GreaterThan"
            },
            "resourceName": "ContosoVM1",
            "resourceType": "microsoft.compute/virtualmachines",
            "resourceRegion": "westus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/automationtest/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "timestamp": "2017-11-16T23:54:03.9517451Z",
            "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/microsoft.insights/alertrules/VMMetricAlert1",
            "name": "VMMetricAlert1",
            "description": "A metric alert for the VM Win2012R2",
            "conditionType": "Metric",
            "subscriptionId": "00000000-0000-0000-0000-000000000000",
            "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "resourceGroupName": "ContosoVM"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    },
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "azure-insights/0.9",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="next-steps"></a>다음 단계

* [새 경고(미리 보기) 환경에 대해 자세히 알아보기](monitoring-overview-unified-alerts.md)
* [Azure Alerts(미리 보기)에서 로그 경고에 대해 알아보기](monitor-alerts-unified-log.md)
* [Azure에서 경고에 대해 알아보기](monitoring-overview-alerts.md)