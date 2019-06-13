---
title: Azure Monitor를 사용하여 데이터 팩터리 모니터링 | Microsoft Docs
description: Azure Data Factory 정보가 있는 진단 로그를 사용하도록 설정하여 Azure Monitor를 통해 Data Factory 파이프라인을 모니터링하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: shlo
ms.openlocfilehash: e96e462709ab0c715c831bd10c628869d5c617fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60319228"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Azure Monitor를 사용하여 데이터 팩터리 경고 및 모니터링
클라우드 애플리케이션은 이동하는 부분이 많아 복잡합니다. 모니터링은 애플리케이션을 유지하고 정상 상태에서 실행할 수 있는 데이터를 제공합니다. 또한 잠재적 문제를 방지하거나 지난 문제를 해결할 수 있습니다. 또한 애플리케이션에 대해 깊이 이해하는 데 모니터링 데이터를 사용할 수 있습니다. 이러한 정보를 사용하면 애플리케이션 성능 또는 유지 관리 편의성을 향상시키거나 그렇지 않으면 수동 개입이 필요한 작업을 자동화할 수 있습니다.

Azure Monitor는 대부분의 Microsoft Azure 서비스에 대한 기본 수준의 인프라 메트릭과 로그를 제공합니다. 자세한 내용은 [모니터링 개요](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)를 참조하세요. Azure 진단 로그는 해당 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공하는 리소스에서 내보낸 로그입니다. Data Factory는 Azure Monitor에서 진단 로그를 출력합니다.

## <a name="persist-data-factory-data"></a>Data Factory 데이터 저장
Data Factory는 45일 동안 파이프라인 실행 데이터를 저장하기만 합니다. 45일 넘게 파이프라인 실행 데이터를 저장하려는 경우, Azure Monitor를 사용하여 분석할 진단 로그를 라우팅할 뿐만 아니라 스토리지 계정에 저장하여 선택한 기간의 팩터리 정보를 유지할 수 있습니다.

## <a name="diagnostic-logs"></a>진단 로그

* 감사 또는 수동 검사를 위해 **Storage 계정** 에 저장합니다. 진단 설정을 사용하여 보존 기간(일)을 지정할 수 있습니다.
* 타사 서비스 또는 사용자 지정 분석 솔루션(예: Power BI)에서 수집할 수 있도록 진단 로그를 **Event Hubs**로 스트리밍합니다.
* **Log Analytics**를 사용하여 분석

로그를 내보내는 리소스와 동일한 구독에 있지 않은 스토리지 계정 또는 이벤트 허브 네임스페이스를 사용할 수 있습니다. 설정을 구성하는 사용자는 두 구독 모두에 대해 적절한 RBAC(역할 기반 액세스 제어) 액세스 권한을 가지고 있어야 합니다.

## <a name="set-up-diagnostic-logs"></a>진단 로그 설정

### <a name="diagnostic-settings"></a>진단 설정
진단 설정을 사용하여 비계산 리소스에 대한 진단 로그를 구성합니다. 리소스 제어에 대한 진단 설정은 다음과 같습니다.

* (저장소 계정, Event Hubs 또는 Azure Monitor 로그) 진단 로그를 보낼 위치입니다.
* 보낼 로그 범주.
* 각 로그 범주를 스토리지 계정에 보존해야 하는 기간.
* 보존 기간이 0일이라는 것은 로그가 영원히 보관된다는 의미입니다. 그렇지 않은 경우 값은 1에서 2147483647 사이의 숫자일 수 있습니다.
* 보존 정책이 설정 되었지만 저장소 계정에 로그를 저장 되지 않음 (예:만 Event Hubs 또는 Azure Monitor 로그 옵션을 선택)을 하는 경우 보존 정책은 효과가 없습니다.
* 보존 정책은 매일 적용되므로 하루의 마지막에(UTC) 보존 정책이 지난 날의 로그가 삭제됩니다. 예를 들어, 하루의 보존 정책이 있는 경우 오늘 날짜가 시작될 때 하루 전의 로그가 삭제됩니다.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>REST API를 통해 진단 로그를 사용하도록 설정

Azure Monitor REST API에서 진단 설정 만들기 또는 업데이트

**요청**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**헤더**
* `{api-version}`을 `2016-09-01`로 바꿉니다.
* `{resource-id}`를 진단 설정을 편집하려는 리소스에 대한 리소스 ID로 바꿉니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/manage-resource-groups-portal.md)를 참조하세요.
* `Content-Type` 헤더를 `application/json`으로 설정합니다.
* 권한 부여 헤더를 Azure Active Directory에서 가져온 JSON 웹 토큰으로 설정합니다. 자세한 내용은 [요청 인증](../active-directory/develop/authentication-scenarios.md)을 참조하세요.

**본문**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| 자산 | Type | 설명 |
| --- | --- | --- |
| storageAccountId |문자열 | 진단 로그를 보내려는 스토리지 계정의 리소스 ID입니다. |
| serviceBusRuleId |문자열 | 진단 로그 스트리밍에 대해 Event Hubs를 만들려는 서비스 버스 네임스페이스의 서비스 버스 규칙 ID입니다. 규칙 ID의 형식은 "{서비스 버스 리소스 ID}/authorizationrules/{키 이름}"입니다.|
| workspaceId | 복합 형식 | 메트릭 시간 조직 및 해당 보존 정책의 배열입니다. 현재 이 속성은 비어 있습니다. |
|metrics| 호출된 파이프라인에 전달될 파이프라인 실행의 매개 변수 값| 매개 변수 이름을 인수 값에 매핑하는 JSON 개체입니다. |
| logs| 복합 형식| 리소스 종류에 대한 진단 로그 범주의 이름입니다. 리소스에 대한 진단 로그 범주 목록을 가져오기 위해 먼저 GET 진단 설정 작업을 수행합니다. |
| category| 문자열| 로그 범주 및 해당 보존 정책의 배열입니다. |
| timeGrain | 문자열 | ISO 8601 기간 형식으로 캡처된 메트릭의 세분성입니다. PT1M(1분)이어야 합니다.|
| enabled| BOOLEAN | 이 리소스에 대한 해당 메트릭 또는 로그 범주의 수집을 사용할지 여부를 지정합니다.|
| retentionPolicy| 복합 형식| 메트릭 또는 로그 범주에 대한 보존 정책을 설명합니다. 저장소 계정 옵션에만 사용됩니다.|
| days| Int| 메트릭 또는 로그를 보존할 기간(일)입니다. 0 값은 로그를 무기한 보존합니다. 저장소 계정 옵션에만 사용됩니다. |

**응답**

200 정상


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

Azure Monitor REST API에서 진단 설정에 대한 정보 가져오기

**요청**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**헤더**
* `{api-version}`을 `2016-09-01`로 바꿉니다.
* `{resource-id}`를 진단 설정을 편집하려는 리소스에 대한 리소스 ID로 바꿉니다. 자세한 내용은 '리소스 그룹을 사용하여 Azure 리소스 관리'를 참조하세요.
* `Content-Type` 헤더를 `application/json`으로 설정합니다.
* 권한 부여 헤더를 Azure Active Directory에서 가져온 JSON 웹 토큰으로 설정합니다. 자세한 내용은 '요청 인증'을 참조하세요.

**응답**

200 정상

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
[자세한 내용 참고](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs--events"></a>로그 및 이벤트의 스키마

### <a name="activity-run-logs-attributes"></a>활동 실행 로그 특성

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| 자산 | Type | 설명 | 예 |
| --- | --- | --- | --- |
| Level |문자열 | 진단 로그 수준입니다. 수준 4는 항상 활동 실행 로그에 대한 경우입니다. | `4`  |
| CorrelationId |문자열 | 엔드투엔드 특정 요청을 추적하는 고유 ID | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| 실시간 | 문자열 | 시간을 UTC 형식으로 이벤트의 시간 `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| 문자열| 활동 실행 ID입니다. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| 문자열| 파이프라인 실행 ID입니다. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|ResourceId| 문자열 | 데이터 팩터리 리소스에 대한 연결된 리소스 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| 문자열 | 진단 로그의 범주입니다. 이 속성을 "ActivityRuns"로 설정합니다. | `ActivityRuns` |
|level| 문자열 | 진단 로그 수준입니다. 이 속성을 "Informational"로 설정합니다. | `Informational` |
|operationName| 문자열 |상태가 있는 활동 이름입니다. 상태가 시작 하트비트이면 `MyActivity -`입니다. 상태가 종료 하트비트이면 최종 상태가 있는 `MyActivity - Succeeded`입니다 | `MyActivity - Succeeded` |
|pipelineName| 문자열 | 파이프라인 이름입니다. | `MyPipeline` |
|activityName| 문자열 | 작업의 이름 | `MyActivity` |
|start| 문자열 | 시간 간격에 속한 활동 실행의 시작 시간(UTC 형식)입니다. | `2017-06-26T20:55:29.5007959Z`|
|end| 문자열 | 시간 간격에 속한 활동 실행의 종료 시간(UTC 형식)입니다. 활동이 아직 종료되지 않은 경우(활동 시작에 대한 진단 로그) 기본값(`1601-01-01T00:00:00Z`)이 설정됩니다.  | `2017-06-26T20:55:29.5007959Z` |

### <a name="pipeline-run-logs-attributes"></a>파이프라인 실행 로그 특성

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| 자산 | Type | 설명 | 예 |
| --- | --- | --- | --- |
| Level |문자열 | 진단 로그 수준입니다. 수준 4는 활동 실행 로그에 대한 경우입니다. | `4`  |
| CorrelationId |문자열 | 엔드투엔드 특정 요청을 추적하는 고유 ID | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| 실시간 | 문자열 | 시간을 UTC 형식으로 이벤트의 시간 `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| 문자열| 파이프라인 실행 ID입니다. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|ResourceId| 문자열 | 데이터 팩터리 리소스에 대한 연결된 리소스 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| 문자열 | 진단 로그의 범주입니다. 이 속성을 "PipelineRuns"로 설정합니다. | `PipelineRuns` |
|level| 문자열 | 진단 로그 수준입니다. 이 속성을 "Informational"로 설정합니다. | `Informational` |
|operationName| 문자열 |상태가 있는 파이프라인 이름입니다. 파이프라인 실행이 완료되면 최종 상태가 있는 "Pipeline - Succeeded"입니다.| `MyPipeline - Succeeded` |
|pipelineName| 문자열 | 파이프라인 이름입니다. | `MyPipeline` |
|start| 문자열 | 시간 간격에 속한 활동 실행의 시작 시간(UTC 형식)입니다. | `2017-06-26T20:55:29.5007959Z`|
|end| 문자열 | 시간 간격에 속한 활동 실행의 종료 시간(UTC 형식)입니다. 활동이 아직 종료되지 않은 경우(활동 시작에 대한 진단 로그) 기본값(`1601-01-01T00:00:00Z`)이 설정됩니다.  | `2017-06-26T20:55:29.5007959Z` |
|status| 문자열 | 파이프라인 실행의 최종 상태(Succeeded 또는 Failed )입니다. | `Succeeded`|

### <a name="trigger-run-logs-attributes"></a>트리거 실행 로그 특성

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| 자산 | Type | 설명 | 예 |
| --- | --- | --- | --- |
| Level |문자열 | 진단 로그 수준입니다. 활동 실행 로그에 대해 수준 4로 설정합니다. | `4`  |
| CorrelationId |문자열 | 엔드투엔드 특정 요청을 추적하는 고유 ID | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| 실시간 | 문자열 | 시간을 UTC 형식으로 이벤트의 시간 `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| 문자열| 트리거 실행 ID입니다. | `08587023010602533858661257311` |
|ResourceId| 문자열 | 데이터 팩터리 리소스에 대한 연결된 리소스 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| 문자열 | 진단 로그의 범주입니다. 이 속성을 "PipelineRuns"로 설정합니다. | `PipelineRuns` |
|level| 문자열 | 진단 로그 수준입니다. 이 속성을 "Informational"로 설정합니다. | `Informational` |
|operationName| 문자열 |트리거가 성공적으로 시작되었는지 여부를 나타내는 최종 상태가 있는 트리거 이름입니다. 하트비트에 성공하면 "MyTrigger - Succeeded"입니다.| `MyTrigger - Succeeded` |
|triggerName| 문자열 | 트리거 이름입니다. | `MyTrigger` |
|triggerType| 문자열 | 트리거 유형(Manual Trigger 또는 Schedule Trigger)입니다. | `ScheduleTrigger` |
|triggerEvent| 문자열 | 트리거 이벤트입니다. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| 문자열 | 시간 간격에서 발생한 트리거의 시작 시간(UTC 형식)입니다. | `2017-06-26T20:55:29.5007959Z`|
|status| 문자열 | 트리거가 성공적으로 시작되었는지 여부에 대한 최종 상태(Succeeded 또는 Failed)입니다. | `Succeeded`|

## <a name="metrics"></a>metrics

Azure 모니터에서는 원격 분석을 사용하여 Azure에서 워크로드의 상태와 성능에 대한 정보를 구할 수 있습니다. Azure 원격 분석 데이터의 가장 중요한 유형은 대부분의 Azure 리소스에서 내보내는 메트릭(성능 카운터라고도 함)입니다. Azure Monitor는 모니터링 및 문제 해결을 위해 이러한 메트릭을 구성 및 사용하는 몇 가지 방법을 제공합니다.

ADFV2에서 내보내는 메트릭은 다음과 같습니다.

| **메트릭**           | **메트릭 표시 이름**         | **단위** | **집계 유형** | **설명**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | 성공한 파이프라인 실행 메트릭 | 카운트    | 합계                | 1분 기간 내에 성공한 총 파이프라인 실행 수입니다. |
| PipelineFailedRuns   | 실패한 파이프라인 실행 메트릭    | 카운트    | 합계                | 1분 기간 내에 실패한 총 파이프라인 실행 수입니다.    |
| ActivitySucceededRuns | 성공한 활동 실행 메트릭 | 카운트    | 합계                | 1분 기간 내에 성공한 총 활동 실행 수입니다.  |
| ActivityFailedRuns   | 실패한 활동 실행 메트릭    | 카운트    | 합계                | 1분 기간 내에 실패한 총 활동 실행 수입니다.     |
| TriggerSucceededRuns | 성공한 트리거 실행 메트릭  | 카운트    | 합계                | 1분 기간 내에 성공한 총 트리거 실행 수입니다.   |
| TriggerFailedRuns    | 실패한 트리거 실행 메트릭     | 카운트    | 합계                | 1분 기간 내에 실패한 총 트리거 실행 수입니다.      |

메트릭에 액세스하려면 다음 문서의 지침을 참조하세요. https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Azure Monitor를 사용하여 Data Factory 메트릭 모니터링

Azure Monitor와 Azure Data Factory 통합을 사용하여 Azure Monitor로 데이터를 라우팅할 수 있습니다. 이 통합은 다음과 같은 시나리오에서 유용합니다.

1.  Data Factory에 의해 Azure Monitor에 게시하는 풍부한 메트릭 집합에 대해 복잡한 쿼리를 작성하려고 합니다. Azure Monitor를 통해 이러한 쿼리에서 사용자 지정 경고를 만들 수도 있습니다.

2.  데이터 팩터리를 모니터링하려고 합니다. 여러 데이터 팩터리에서 하나의 Azure Monitor 작업 영역으로 데이터를 라우팅할 수 있습니다.

7분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>진단 설정 및 작업 영역 구성

데이터 팩터리에 대한 진단 설정 사용

1.  **Azure Monitor** -> **진단 설정**을 선택하고 -> 데이터 팩터리를 선택하고 -> 진단을 설정합니다.

    ![monitor-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  작업 영역의 구성을 포함한 진단 설정을 제공합니다.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Azure Marketplace에서 Azure Data Factory Analytics 설치

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

**만들기**를 클릭하고 작업 영역 및 작업 영역 설정을 선택합니다.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Data Factory 메트릭 모니터링

**Azure Data Factory Analytics**를 설치하면 다음과 같은 메트릭이 설정된 기본 보기 집합이 생성됩니다.

- ADF 실행- 1) Data Factory의 파이프라인 실행

- ADF 실행- 2) Data Factory의 활동 실행

- ADF 실행- 3) Data Factory의 트리거 실행

- ADF 오류- 1) Data Factory의 상위 10개 파이프라인 오류

- ADF 오류- 2) Data Factory의 상위 10개 활동 실행

- ADF 오류- 3) Data Factory의 상위 10개 트리거 오류

- ADF 통계- 1) 형식별 활동 실행

- ADF 통계- 2) 형식별 트리거 실행

- ADF 통계- 3) 최대 파이프라인 실행 기간

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

위의 메트릭을 시각화하고, 이러한 메트릭에 숨겨진 쿼리를 확인하며, 쿼리를 편집하고, 경고를 생성하는 등의 작업을 수행할 수 있습니다.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="alerts"></a>경고

Azure Portal에 로그인하고 **모니터 -&gt; 경고**를 클릭하여 경고를 만듭니다.

![포털 메뉴의 경고](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>경고 만들기

1.  **+ 새로운 경고 규칙**을 클릭하여 새 경고를 만듭니다.

    ![새 경고 규칙](media/monitor-using-azure-monitor/alerts_image4.png)

2.  **경고 조건**을 정의합니다.

    > [!NOTE]
    > **리소스 종류별로 필터링**에서 **모두**를 선택해야 합니다.

    ![경고 조건, 1/3 화면](media/monitor-using-azure-monitor/alerts_image5.png)

    ![경고 조건, 2/3 화면](media/monitor-using-azure-monitor/alerts_image6.png)

    ![경고 조건, 3/3 화면](media/monitor-using-azure-monitor/alerts_image7.png)

3.  **경고 세부 정보**를 정의합니다.

    ![경고 세부 정보](media/monitor-using-azure-monitor/alerts_image8.png)

4.  **작업 그룹**을 정의합니다.

    ![작업 그룹, 1/4 화면](media/monitor-using-azure-monitor/alerts_image9.png)

    ![작업 그룹, 2/4 화면](media/monitor-using-azure-monitor/alerts_image10.png)

    ![작업 그룹, 3/4 화면](media/monitor-using-azure-monitor/alerts_image11.png)

    ![작업 그룹, 4/4 화면](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>다음 단계

코드를 사용한 파이프라인 모니터링 및 관리에 대한 자세한 내용은 [프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md) 문서를 참조하세요.
