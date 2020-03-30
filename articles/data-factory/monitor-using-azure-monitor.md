---
title: Azure Monitor를 사용하여 데이터 팩터리 모니터링
description: Azure 모니터를 사용하여 데이터 팩터리의 정보로 진단 로그를 사용하도록 설정하여 /Azure Data Factory 파이프라인을 모니터링하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 8325b4ef6b89a76eeec418386cec4922cb5916b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979159"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Azure 모니터를 사용하여 데이터 팩터리에 대한 경고 및 모니터링

클라우드 응용 프로그램은 복잡하고 많은 움직이는 부분이 있습니다. 모니터는 응용 프로그램이 정상 상태로 유지되고 실행되도록 하는 데 도움이 되는 데이터를 제공합니다. 또한 모니터를 사용하면 잠재적인 문제를 방지하고 과거의 문제를 해결할 수 있습니다.

모니터링 데이터를 사용하여 애플리케이션에 대한 심층적인 통찰력을 얻을 수 있습니다. 이 지식은 응용 프로그램 성능및 유지 관리 가능성을 개선하는 데 도움이 됩니다. 또한 수동 작업이 필요한 작업을 자동화하는 데도 도움이 됩니다.

Azure Monitor는 대부분의 Azure 서비스에 대한 기본 수준 인프라 메트릭 및 로그를 제공합니다. Azure 진단 로그는 리소스에서 내보내며, 해당 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공합니다. Azure 데이터 팩터리는 모니터에 진단 로그를 씁니다.

자세한 내용은 [Azure 모니터 개요를](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)참조하십시오.

## <a name="keeping-azure-data-factory-data"></a>Azure 데이터 팩터리 데이터 유지

Data Factory는 파이프라인 실행 데이터를 45일 동안만 저장합니다. 해당 데이터를 더 오랫동안 유지하려면 모니터를 사용합니다. 모니터를 사용하면 분석을 위해 진단 로그를 라우팅할 수 있습니다. 선택한 기간에 대한 공장 정보를 갖도록 저장소 계정에 보관할 수도 있습니다.

## <a name="diagnostic-logs"></a>진단 로그

* 감사 또는 수동 검사를 위해 진단 로그를 저장소 계정에 저장합니다. 진단 설정을 사용하여 보존 시간을 일 수 있도록 지정할 수 있습니다.
* 로그를 Azure 이벤트 허브로 스트리밍합니다. 로그는 파트너 서비스 또는 Power BI와 같은 사용자 지정 분석 솔루션에 입력됩니다.
* 로그 분석으로 로그를 분석합니다.

로그를 내하는 리소스의 구독에 없는 저장소 계정 또는 이벤트 허브 네임스페이스를 사용할 수 있습니다. 설정을 구성하는 사용자는 두 구독모두에 대한 적절한 RBAC 액세스 권한이 있어야 합니다.

## <a name="set-up-diagnostic-logs"></a>진단 로그 설정

### <a name="diagnostic-settings"></a>진단 설정

진단 설정을 사용하여 계산되지 않은 리소스에 대한 진단 로그를 구성합니다. 리소스 제어 설정에는 다음과 같은 기능이 있습니다.

* 진단 로그가 전송되는 위치를 지정합니다. 예를 들어 Azure 저장소 계정, Azure 이벤트 허브 또는 모니터 로그를 들 수 있습니다.
* 전송되는 로그 범주를 지정합니다.
* 각 로그 범주를 저장소 계정에 보관해야 하는 기간지정합니다.
* 보존 기간이 0일이라는 것은 로그가 영원히 보관된다는 의미입니다. 그렇지 않으면 값은 1부터 2,147,483,647까지의 일 수일수일 수 있습니다.
* 보존 정책이 설정되어 있지만 저장소 계정에 로그를 저장하지 않으면 보존 정책이 영향을 주지 않습니다. 예를 들어 이벤트 허브 또는 모니터 로그 옵션만 선택한 경우 이 조건이 발생할 수 있습니다.
* 보존 정책은 하루에 적용됩니다. 일 사이의 경계는 자정 조정 된 유니버설 타임 (UTC)에서 발생합니다. 하루가 끝나면 보존 정책을 벗어난 날의 로그가 삭제됩니다. 예를 들어 하루 간의 보존 정책이 있는 경우 오늘 초에 어제 이전의 로그가 삭제됩니다.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Azure 모니터 REST API를 통해 진단 로그 사용

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>모니터 REST API에서 진단 설정 만들기 또는 업데이트

##### <a name="request"></a>요청

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>headers

* `{api-version}`을 `2016-09-01`로 바꿉니다.
* 진단 `{resource-id}` 설정을 편집할 리소스의 ID로 바꿉니다. 자세한 내용은 [리소스 그룹 사용을 참조하여 Azure 리소스를 관리합니다.](../azure-resource-manager/management/manage-resource-groups-portal.md)
* `Content-Type` 헤더를 `application/json`으로 설정합니다.
* Azure Active Directory(Azure AD)에서 얻은 JSON 웹 토큰으로 권한 부여 헤더를 설정합니다. 자세한 내용은 [요청 인증](../active-directory/develop/authentication-scenarios.md)을 참조하십시오.

##### <a name="body"></a>본문

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

| 속성 | Type | Description |
| --- | --- | --- |
| **storageAccountId** |String | 진단 로그를 보낼 저장소 계정의 리소스 ID입니다. |
| **serviceBusRuleId** |String | 스트리밍 진단 로그를 위해 이벤트 허브를 만들려는 서비스 버스 네임스페이스의 서비스 버스 규칙 ID입니다. 규칙 ID에는 형식이 `{service bus resource ID}/authorizationrules/{key name}`있습니다.|
| **작업 공간ID** | 복합 형식 | 메트릭 시간 그레인 및 해당 보존 정책의 배열입니다. 이 속성의 값은 비어 있습니다. |
|**메트릭**| 호출된 파이프라인에 전달될 파이프라인 실행의 매개 변수 값| 매개 변수 이름을 인수 값에 매핑하는 JSON 개체입니다. |
| **로그**| 복합 형식| 리소스 유형에 대한 진단 로그 범주의 이름입니다. 리소스에 대한 진단 로그 범주 목록을 얻으려면 GET 진단 설정 작업을 수행합니다. |
| **범주**| String| 로그 범주 및 보존 정책의 배열입니다. |
| **시간 그레인** | String | ISO 8601 기간 형식으로 캡처되는 메트릭의 세분성입니다. 속성 값은 `PT1M`1분을 지정하는 값이어야 합니다. |
| **사용**| 부울 | 이 리소스에 대해 메트릭 또는 로그 범주의 수집이 활성화되어 있는지 여부를 지정합니다. |
| **retentionPolicy**| 복합 형식| 메트릭 또는 로그 범주에 대한 보존 정책을 설명합니다. 이 속성은 저장소 계정에만 사용됩니다. |
|**일**| Int| 메트릭 또는 로그를 유지할 일 수입니다. 속성 값이 0이면 로그는 영원히 유지됩니다. 이 속성은 저장소 계정에만 사용됩니다. |

##### <a name="response"></a>응답

200 정상.


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

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>모니터 REST API에서 진단 설정에 대한 정보 얻기

##### <a name="request"></a>요청

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>headers

* `{api-version}`을 `2016-09-01`로 바꿉니다.
* 진단 `{resource-id}` 설정을 편집할 리소스의 ID로 바꿉니다. 자세한 내용은 [리소스 그룹 사용을 참조하여 Azure 리소스를 관리합니다.](../azure-resource-manager/management/manage-resource-groups-portal.md)
* `Content-Type` 헤더를 `application/json`으로 설정합니다.
* 권한 부여 헤더를 Azure AD에서 얻은 JSON 웹 토큰으로 설정합니다. 자세한 내용은 [요청 인증](../active-directory/develop/authentication-scenarios.md)을 참조하십시오.

##### <a name="response"></a>응답

200 정상.

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
자세한 내용은 [진단 설정](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)을 참조하십시오.

## <a name="schema-of-logs-and-events"></a>로그 및 이벤트 스키마

### <a name="monitor-schema"></a>모니터 스키마

#### <a name="activity-run-log-attributes"></a>활동 실행 로그 특성

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

| 속성 | Type | Description | 예제 |
| --- | --- | --- | --- |
| **수준** |String | 진단 로그의 수준입니다. 활동 실행 로그의 경우 속성 값을 4로 설정합니다. | `4` |
| **Correlationid** |String | 특정 요청을 추적하기 위한 고유 ID입니다. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **시간** | String | 시간 범위 UTC 형식의 이벤트 `YYYY-MM-DDTHH:MM:SS.00000Z`시간입니다. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| 활동의 ID가 실행됩니다. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| 파이프라인의 ID가 실행됩니다. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Resourceid**| String | 데이터 팩터리 리소스와 연결된 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**범주**| String | 진단 로그의 범주입니다. 속성 값을 `ActivityRuns`로 설정합니다. | `ActivityRuns` |
|**수준**| String | 진단 로그의 수준입니다. 속성 값을 `Informational`로 설정합니다. | `Informational` |
|**작업이름**| String | 상태가 있는 활동의 이름입니다. 활동이 시작 하트비트인 경우 속성 값은 입니다. `MyActivity -` 활동이 끝 하트비트인 경우 속성 값은 입니다. `MyActivity - Succeeded` | `MyActivity - Succeeded` |
|**pipelineName**| String | 파이프라인의 이름 | `MyPipeline` |
|**activityName**| String | 작업의 이름입니다. | `MyActivity` |
|**start**| String | 활동의 시작 시간은 시간 범위 UTC 형식으로 실행됩니다. | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | 활동의 종료 시간은 시간 범위 UTC 형식으로 실행됩니다. 진단 로그에 활동이 시작되었지만 아직 종료되지 않았다는 것을 `1601-01-01T00:00:00Z`보여 주는 경우 속성 값은 입니다. | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>파이프라인 실행 로그 특성

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

| 속성 | Type | Description | 예제 |
| --- | --- | --- | --- |
| **수준** |String | 진단 로그의 수준입니다. 활동 실행 로그의 경우 속성 값을 4로 설정합니다. | `4` |
| **Correlationid** |String | 특정 요청을 추적하기 위한 고유 ID입니다. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **시간** | String | 시간 범위 UTC 형식의 이벤트 `YYYY-MM-DDTHH:MM:SS.00000Z`시간입니다. | `2017-06-28T21:00:27.3534352Z` |
|**런Id**| String| 파이프라인의 ID가 실행됩니다. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Resourceid**| String | 데이터 팩터리 리소스와 연결된 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**범주**| String | 진단 로그의 범주입니다. 속성 값을 `PipelineRuns`로 설정합니다. | `PipelineRuns` |
|**수준**| String | 진단 로그의 수준입니다. 속성 값을 `Informational`로 설정합니다. | `Informational` |
|**작업이름**| String | 상태와 함께 파이프라인의 이름입니다. 파이프라인 실행이 완료되면 속성 값은 `Pipeline - Succeeded`입니다. | `MyPipeline - Succeeded`. |
|**pipelineName**| String | 파이프라인의 이름 | `MyPipeline` |
|**start**| String | 활동의 시작 시간은 시간 범위 UTC 형식으로 실행됩니다. | `2017-06-26T20:55:29.5007959Z`. |
|**end**| String | 활동의 종료 시간은 시간 범위 UTC 형식으로 실행됩니다. 진단 로그에 활동이 시작되었지만 아직 종료되지 않은 경우 `1601-01-01T00:00:00Z`속성 값은 입니다.  | `2017-06-26T20:55:29.5007959Z` |
|**상태**| String | 파이프라인 실행의 최종 상태입니다. 가능한 속성 `Succeeded` 값은 및 `Failed` | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>트리거 실행 로그 특성

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

| 속성 | Type | Description | 예제 |
| --- | --- | --- | --- |
| **수준** |String | 진단 로그의 수준입니다. 활동 실행 로그의 경우 속성 값을 4로 설정합니다. | `4` |
| **Correlationid** |String | 특정 요청을 추적하기 위한 고유 ID입니다. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **시간** | String | 시간 범위 UTC 형식의 이벤트 `YYYY-MM-DDTHH:MM:SS.00000Z`시간입니다. | `2017-06-28T21:00:27.3534352Z` |
|**트리거 ID**| String| 트리거의 ID가 실행됩니다. | `08587023010602533858661257311` |
|**Resourceid**| String | 데이터 팩터리 리소스와 연결된 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**범주**| String | 진단 로그의 범주입니다. 속성 값을 `PipelineRuns`로 설정합니다. | `PipelineRuns` |
|**수준**| String | 진단 로그의 수준입니다. 속성 값을 `Informational`로 설정합니다. | `Informational` |
|**작업이름**| String | 트리거가 성공적으로 발생했는지 여부를 나타내는 최종 상태가 있는 트리거의 이름입니다. 하트비트가 성공한 경우 속성 값은 입니다. `MyTrigger - Succeeded` | `MyTrigger - Succeeded` |
|**트리거 이름**| String | 트리거의 이름입니다. | `MyTrigger` |
|**트리거 유형**| String | 트리거의 유형입니다. 가능한 속성 `Manual Trigger` 값은 및 `Schedule Trigger` | `ScheduleTrigger` |
|**triggerEvent**| String | 트리거의 이벤트입니다. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | 시간 범위 UTC 형식으로 트리거 가 시작되는 시간입니다. | `2017-06-26T20:55:29.5007959Z`|
|**상태**| String | 트리거가 성공적으로 발생했는지 여부를 보여주는 최종 상태입니다. 가능한 속성 `Succeeded` 값은 및 `Failed` | `Succeeded`|

### <a name="log-analytics-schema"></a>로그 분석 스키마

Log Analytics는 다음과 같은 예외를 제외하고 모니터에서 스키마를 상속합니다.

* 각 열 이름의 첫 번째 문자는 대문자로 표시됩니다. 예를 들어 모니터의 열 이름 "correlationId"는 로그 분석에서 "상관 관계 Id"입니다.
* "수준" 열이 없습니다.
* 동적 "속성" 열은 다음과 같은 동적 JSON Blob 유형으로 유지됩니다.

    | Azure 모니터 열 | 로그 분석 칼럼 | Type |
    | --- | --- | --- |
    | $.속성. 사용자 속성 | 사용자 속성 | 동적 |
    | $.속성. 주석 | 주석 | 동적 |
    | $.속성. 입력 | 입력 | 동적 |
    | $.속성. 출력 | 출력 | 동적 |
    | $.속성. 오류 오류 코드 | ErrorCode | int |
    | $.속성. 오류.메시지 | ErrorMessage | 문자열 |
    | $.속성. 오류 | Error | 동적 |
    | $.속성. 전임자 | 전임자 | 동적 |
    | $.속성. 매개 변수 | 매개 변수 | 동적 |
    | $.속성. 시스템매개 변수 | SystemParameters | 동적 |
    | $.속성. 태그 | 태그들 | 동적 |
    
## <a name="metrics"></a>메트릭

모니터를 사용하면 Azure 워크로드의 성능및 상태를 확인할 수 있습니다. 모니터 데이터의 가장 중요한 유형은 성능 카운터라고도 하는 메트릭입니다. 메트릭은 대부분의 Azure 리소스에서 내보내입니다. 모니터는 모니터링 및 문제 해결을 위해 이러한 메트릭을 구성하고 사용하는 여러 가지 방법을 제공합니다.

Azure 데이터 팩터리 버전 2는 다음 메트릭을 내보시작합니다.

| **메트릭**           | **메트릭 표시 이름**         | **단위** | **집계 유형** | **설명**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | 성공한 파이프라인 실행 메트릭 | 개수    | 합계                | 1분 내에 성공한 총 파이프라인 실행 수입니다. |
| PipelineFailedRuns   | 실패한 파이프라인 실행 메트릭    | 개수    | 합계                | 1분 내에 실패한 총 파이프라인 실행 수입니다.    |
| ActivitySucceededRuns | 성공한 활동 실행 메트릭 | 개수    | 합계                | 1분 내에 성공한 총 활동 실행 수입니다.  |
| ActivityFailedRuns   | 실패한 활동 실행 메트릭    | 개수    | 합계                | 1분 내에 실패한 총 활동 실행 수입니다.     |
| TriggerSucceededRuns | 성공한 트리거 실행 메트릭  | 개수    | 합계                | 1분 내에 성공한 총 트리거 실행 수입니다.   |
| TriggerFailedRuns    | 실패한 트리거 실행 메트릭     | 개수    | 합계                | 1분 내에 실패한 총 트리거 실행 수입니다.      |

메트릭에 액세스하려면 Azure Monitor 데이터 플랫폼에서 지침을 [완료합니다.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)

> [!NOTE]
> 완료되고 트리거된 활동 및 파이프라인 실행 이벤트만 내보전됩니다. 진행 중이며 샌드박스/디버그 실행은 **내보내지지 않습니다.** 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Azure 모니터를 사용하여 데이터 팩터리 메트릭 모니터링

모니터와 데이터 팩터리 통합을 사용하여 데이터를 모니터로 라우팅할 수 있습니다. 이 통합은 다음과 같은 시나리오에서 유용합니다.

* 데이터 팩터리에서 모니터링할 메트릭에 의해 게시되는 다양한 메트릭 집합에 복잡한 쿼리를 작성하려고 합니다. 모니터를 통해 이러한 쿼리에 사용자 지정 경고를 만들 수 있습니다.

* 데이터 팩터리를 모니터링하려고 합니다. 여러 데이터 팩터의 데이터를 단일 모니터 작업 영역으로 라우팅할 수 있습니다.

7분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>진단 설정 및 작업 영역 구성

데이터 팩터리의 진단 설정을 만들거나 추가합니다.

1. 포털에서 모니터로 이동합니다. **설정** > **진단 설정을**선택합니다.

1. 진단 설정을 설정할 데이터 팩터리를 선택합니다.

1. 선택한 데이터 팩터리에 설정이 없으면 설정을 만들라는 메시지가 표시됩니다. **진단 켜기**를 선택합니다.

   ![설정이 없는 경우 진단 설정 만들기](media/data-factory-monitor-oms/monitor-oms-image1.png)

   데이터 팩터리에 기존 설정이 있는 경우 데이터 팩터리에서 이미 구성된 설정 목록이 표시됩니다. **진단 설정 추가를**선택합니다.

   ![설정이 있는 경우 진단 설정 추가](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. 이름을 지정하고 **로그 분석으로 보내기를**선택한 다음 **로그 분석 작업 영역에서**작업 영역을 선택합니다.

    ![설정 이름을 지정하고 로그 분석 작업 영역을 선택합니다.](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. **저장**을 선택합니다.

잠시 후 이 데이터 팩터리의 설정 목록에 새 설정이 나타납니다. 새 이벤트 데이터가 생성되는 즉시 진단 로그가 해당 작업 영역으로 스트리밍됩니다. 이벤트가 내보내지는 시점과 로그 애널리틱스에 표시되는 시점 사이에는 최대 15분이 경과할 수 있습니다.

* _리소스별_ 모드에서 Azure 데이터 팩터리의 진단 로그가 _ADFPipelineRun,_ _ADFTriggerRun_및 _ADFActivityRun_ 테이블로 흐릅니다.
* _Azure 진단_ 모드에서 진단 로그는 _AzureDiagnostics_ 테이블로 흐릅니다.

> [!NOTE]
> Azure 로그 테이블에는 500개 이상의 열이 있을 수 없으므로 리소스별 모드를 선택하는 것이 좋습니다. 자세한 내용은 [로그 분석 알려진 제한 을](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics)참조하십시오.

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Azure Marketplace에서 Azure Data Factory Analytics 설치

!["Azure 마켓플레이스"로 이동하여 "분석 필터"를 입력하고 "Azure 데이터 팩토리 분석(미리 보기)"을 선택합니다.](media/data-factory-monitor-oms/monitor-oms-image3.png)

!["Azure 데이터 팩터리 분석(미리 보기)"에 대한 세부 정보](media/data-factory-monitor-oms/monitor-oms-image4.png)

**OMS 작업 영역** 및 **OMS 작업 영역 설정** **만들기를** 선택한 다음 선택합니다.

![새 솔루션 만들기](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>데이터 팩토리 메트릭 모니터링

Azure 데이터 팩터리 분석을 설치하면 다음 메트릭이 활성화되도록 기본 보기 집합이 만들어집니다.

- ADF 실행 - 1) 데이터 팩터리에서 파이프라인 실행
 
- ADF 실행- 2) Data Factory의 활동 실행

- ADF 실행 - 3) 데이터 팩터리에서 트리거 실행

- ADF 오류 - 1) 데이터 팩터리별 상위 10개 파이프라인 오류

- ADF 오류 - 2) 데이터 팩터리에서 실행되는 상위 10개 활동

- ADF 오류 - 3) 데이터 팩터리별 상위 10개 트리거 오류

- ADF 통계 - 1) 유형별로 활동이 실행됩니다.

- ADF 통계 - 2) 유형별로 트리거 실행

- ADF 통계 - 3) 최대 파이프라인 실행 기간

!["통합 문서(미리 보기)" 및 "AzureDataFactoryAnalytics"가 강조 표시된 창](media/data-factory-monitor-oms/monitor-oms-image6.png)

이전 메트릭을 시각화하고, 이러한 메트릭 뒤에 있는 쿼리를 살펴보고, 쿼리를 편집하고, 경고를 만들고, 다른 작업을 수행할 수 있습니다.

![데이터 팩터리에서 실행되는 파이프라인의 그래픽 표현"](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure 데이터 팩터리 분석(미리 보기)은 _리소스별_ 대상 테이블로 진단 로그를 보냅니다. _ADF파이프라인 실행,_ _ADFTriggerRun_및 _ADFActivityRun_: 다음 테이블에 대한 쿼리를 작성할 수 있습니다.

## <a name="alerts"></a>경고

Azure 포털에 로그인하고 > **경고** **모니터링을**선택하여 경고를 만듭니다.

![포털 메뉴의 경고](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>경고 만들기

1. **+ 새로운 경고 규칙**을 선택하여 새 경고를 만듭니다.

    ![새 경고 규칙](media/monitor-using-azure-monitor/alerts_image4.png)

1. 경고 조건을 정의합니다.

    > [!NOTE]
    > 리소스 유형 드롭다운 **목록별로 필터에서** **모두를** 선택해야 합니다.

    !["리소스 선택" 창을 여는 "대상 선택"> "경고 조건 정의" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["경고 조건 정의" >" "신호 논리 구성" 창을 여는 조건 추가"](media/monitor-using-azure-monitor/alerts_image6.png)

    !["신호 유형 구성" 창](media/monitor-using-azure-monitor/alerts_image7.png)

1. 경고 세부 정보를 정의합니다.

    ![경고 세부 정보](media/monitor-using-azure-monitor/alerts_image8.png)

1. 작업 그룹을 정의합니다.

    !['새 작업 그룹'이 강조 표시된 규칙 만들기](media/monitor-using-azure-monitor/alerts_image9.png)

    ![새 작업 그룹 만들기](media/monitor-using-azure-monitor/alerts_image10.png)

    ![이메일, SMS, 푸시 및 음성 구성](media/monitor-using-azure-monitor/alerts_image11.png)

    ![작업 그룹 정의](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>다음 단계
[파이프라인을 프로그래밍 방식으로 모니터링 및 관리](monitor-programmatically.md)
