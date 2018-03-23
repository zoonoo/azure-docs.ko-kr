---
title: Azure Monitor에서 근 실시간 메트릭 경고 | Microsoft Docs
description: 근 실시간 메트릭 경고를 사용하여 1분 간격으로 Azure 리소스 메트릭을 모니터링하는 방법을 알아봅니다.
author: snehithm
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 88995b1f3350fe485e28efccc93779ae0a42eb97
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>근 실시간 메트릭 경고(미리 보기)
이제 Azure Monitor는 근 실시간 메트릭 경고(미리 보기)라는 새로운 유형의 경고를 지원합니다. 이 기능은 현재 공개 미리 보기로 제공됩니다.

근 실시간 메트릭 경고는 몇 가지 점에서 일반 메트릭 경고와 다릅니다.

- **대기 시간 향상**: 근 실시간 메트릭 경고는 1분 간격으로 메트릭 값의 변경 내용을 모니터링할 수 있습니다.
- **메트릭 조건 세부 제어**: 근 실시간 메트릭 경고의 경고 규칙을 다양하게 정의할 수 있습니다. 경고에서 메트릭의 최대, 최소, 평균 및 전체 값을 모니터링할 수 있습니다.
- **로그의 메트릭**: [Log Analytics](../log-analytics/log-analytics-overview.md)로 들어오는 인기 있는 로그 데이터에서 Azure Monitor로 메트릭이 추출되고, 근 실시간으로 경고가 표시될 수 있습니다.
- **여러 가지 메트릭의 결합 모니터링**: 근 실시간 메트릭 경고는 단일 규칙을 가진 다중 메트릭(현재 최대 2개)을 모니터링할 수 있습니다. 두 메트릭이 지정된 기간 동안 해당 임계값을 위반하면 경고가 트리거됩니다.
- **모듈식 알림 시스템**: 근 실시간 메트릭 경고는 [작업 그룹](monitoring-action-groups.md)을 사용합니다. 작업 그룹을 사용하여 모듈식 동작을 만들 수 있습니다. 작업 그룹을 여러 경고 규칙에 다시 사용할 수 있습니다.

> [!NOTE]
> 근 실시간 메트릭 경고는 현재 공개 미리 보기로 제공됩니다. 또한 로그 기능의 메트릭은 *제한된* 공개 미리 보기로 제공됩니다. 기능 및 사용자 환경을 변경할 수 있습니다.
>

## <a name="metrics-and-dimensions-supported"></a>지원되는 메트릭 및 차원
근 실시간 메트릭 경고는 차원을 사용하는 메트릭에 대한 경고를 지원합니다. 차원을 사용하여 메트릭을 적절한 수준으로 필터링할 수 있습니다. 해당 차원과 함께 지원되는 모든 메트릭을 [Azure Monitor - 메트릭 탐색기(미리 보기)](monitoring-metric-charts.md)에서 탐색하고 시각화할 수 있습니다.

다음은 근 실시간 메트릭 경고에서 지원되는 Azure Monitor 기반 메트릭 원본의 전체 목록입니다.

|메트릭 이름/세부 정보  |지원되는 차원  |
|---------|---------|
|Microsoft.ApiManagement/service     | 예        |
|Microsoft.Automation/automationAccounts     |     해당 없음    |
|Microsoft.Automation/automationAccounts     |   해당 없음      |
|Microsoft.Cache/Redis     |    해당 없음     |
|Microsoft.Compute/virtualMachines     |    해당 없음     |
|Microsoft.Compute/virtualMachineScaleSets     |   해당 없음      |
|Microsoft.DataFactory/factories     |   해당 없음      |
|Microsoft.DBforMySQL/servers     |   해당 없음      |
|Microsoft.DBforPostgreSQL/servers     |    해당 없음     |
|Microsoft.EventHub/namespaces     |   해당 없음      |
|Microsoft.Logic/workflows     |     해당 없음    |
|Microsoft.Network/applicationGateways     |    해당 없음     |
|Microsoft.Network/publicipaddresses     |  해당 없음       |
|Microsoft.Search/searchServices     |   해당 없음      |
|Microsoft.ServiceBus/namespaces     |  해당 없음       |
|Microsoft.Storage/storageAccounts     |    예     |
|Microsoft.Storage/storageAccounts/services     |     예    |
|Microsoft.StreamAnalytics/streamingjobs     |  해당 없음       |
|Microsoft.CognitiveServices/accounts     |    해당 없음     |


로그의 메트릭은 현재, 다음과 같은 인기 있는 OMS 로그를 지원합니다.
- Windows 및 Linux 컴퓨터에 대한 [성능 카운터](../log-analytics/log-analytics-data-sources-performance-counters.md)
- 컴퓨터에 대한 하트비트 레코드
- [업데이트 관리](../operations-management-suite/oms-solution-update-management.md) 레코드

다음은 근 실시간 메트릭 경고에서 지원되는 OMS 로그 기반 메트릭 원본의 전체 목록입니다.

메트릭 이름/세부 정보  |지원되는 차원  | 로그 유형  |
|---------|---------|---------|
|Average_Avg. 디스크 초/읽기     |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
| Average_Avg. 디스크 초/쓰기     |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
| Average_Current Disk Queue Length   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
| Average_Disk Reads/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
| Average_Disk Transfers/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
|   Average_% Free Space    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
| Average_Available MBytes     |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
| Average_% Committed Bytes In Use    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
| Average_Bytes Received/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
|  Average_Bytes Sent/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
|  Average_Bytes Total/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
|  Average_% Processor Time    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
|   Average_Processor Queue Length    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Windows 성능 카운터      |
|   Average_% Free Inodes   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Free Space   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Used Inodes  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Used Space   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Disk Read Bytes/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Disk Reads/sec |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Disk Transfers/sec |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Disk Write Bytes/sec   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Disk Writes/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Free Megabytes |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Logical Disk Bytes/sec |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Available Memory |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Available Swap Space |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Used Memory  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Used Swap Space  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Available MBytes Memory    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Available MBytes Swap  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Page Reads/sec |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Page Writes/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Pages/sec  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Used MBytes Swap Space |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Used Memory MBytes |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Total Bytes Transmitted    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Total Bytes Received   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Total Bytes    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Total Packets Transmitted  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Total Packets Received |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Total Rx Errors    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Total Tx Errors    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Total Collisions   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Avg. 디스크 초/읽기 |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Avg. 디스크 초/전송 |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Avg. 디스크 초/쓰기    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Physical Disk Bytes/sec    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Pct Privileged Time    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Pct User Time  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Used Memory kBytes |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Virtual Shared Memory  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% DPC Time |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Idle Time    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Interrupt Time   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% IO Wait Time |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Nice Time    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Privileged Time  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% Processor Time   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_% User Time    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Free Physical Memory   |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Free Space in Paging Files |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Free Virtual Memory    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Processes  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Size Stored In Paging Files    |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Uptime |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Average_Users  |     예 - Computer, ObjectName, InstanceName, CounterPath 및 SourceSystem    |   Linux 성능 카운터      |
|    Heartbeat  |     예 - Computer, OSType, Version 및 SourceComputerId    |   하트비트 레코드 |
|    주 지역에서 |     예 - Computer, Product, Classification, UpdateState, Optional 및 Approved    |   업데이트 관리 |

> [!NOTE]
> 특정 메트릭 및/또는 차원은 선택한 기간에 해당 데이터가 존재할 때만 표시됩니다.

## <a name="create-a-near-real-time-metric-alert"></a>근 실시간 메트릭 경고 만들기
현재는 Azure Portal에서만 근 메트릭 실시간 경고를 만들 수 있습니다. PowerShell, Azure CLI(명령줄 인터페이스) 및 Azure Monitor REST API를 사용한 근 실시간 메트릭 경고 구성은 조만간 지원될 예정입니다.

근 실시간 메트릭 경고를 만드는 환경은 새로운 **경고(미리 보기)** 페이지로 이동되었습니다. 현재 경고 페이지에 **근 실시간 메트릭 경고 추가**가 표시되더라도 **경고(미리 보기)** 페이지로 리디렉션됩니다.

근 실시간 메트릭 경고를 만드는 방법은 [Azure Portal에서 경고 규칙 만들기](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)를 참조하세요.

## <a name="manage-near-real-time-metric-alerts"></a>근 실시간 메트릭 경고 관리
근 실시간 메트릭 경고를 만든 후에는 [Azure Portal에서 경고 관리](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)에 설명된 단계에 따라 경고를 관리할 수 있습니다.

## <a name="payload-schema"></a>페이로드 스키마

POST 작업에는 적절히 구성된 [작업 그룹](monitoring-action-groups.md)이 사용될 때 모든 근 실시간 메트릭 경고에 대해 다음과 같은 JSON 페이로드와 스키마가 포함됩니다.

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

* 새 [경고(미리 보기) 환경](monitoring-overview-unified-alerts.md)에 대해 자세히 알아봅니다.
* [Azure Alerts(미리 보기)의 로그 경고](monitor-alerts-unified-log.md)에 대해 알아봅니다.
* [Azure의 경고](monitoring-overview-alerts.md)에 대해 알아봅니다.
