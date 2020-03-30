---
title: 이벤트 허브로 Azure 플랫폼 로그 스트림
description: Azure 리소스 로그를 이벤트 허브로 스트리밍하여 타사 SIEM 및 기타 로그 분석 솔루션과 같은 외부 시스템으로 데이터를 보내는 방법을 알아봅니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658917"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Azure 플랫폼 로그를 Azure 이벤트 허브로 스트리밍
Azure 활동 로그 및 리소스 로그를 포함한 [Azure의 플랫폼 로그는](platform-logs-overview.md) Azure 리소스 및 해당 플랫폼에 대한 자세한 진단 및 감사 정보를 제공합니다.  이 문서에서는 이벤트 허브에 스트리밍 플랫폼 로그를 사용하여 타사 SIEM 및 기타 로그 분석 솔루션과 같은 외부 시스템으로 데이터를 전송합니다.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>이벤트 허브로 전송된 플랫폼 로그를 사용하여 수행할 수 있는 일
Azure에서 이벤트 허브로 플랫폼 로그를 스트리밍하여 다음 기능을 제공합니다.

* **제3자 로깅 및 원격 분석 시스템으로 로그 스트리밍** - 모든 플랫폼 로그를 단일 이벤트 허브로 스트리밍하여 로그 데이터를 타사 SIEM 또는 로그 분석 도구로 파이프합니다.
  
* **사용자 지정 원격 분석 및 로깅 플랫폼 구축** - 이벤트 허브의 확장성이 뛰어난 게시-구독 특성을 통해 플랫폼 로그를 사용자 지정 원격 분석 플랫폼으로 유연하게 통합할 수 있습니다. 자세한 내용은 [Azure 이벤트 허브에서 전역 규모의 원격 분석 플랫폼 설계 및 크기 조정을](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) 참조하십시오.

* **Power BI로 데이터를 스트리밍하여 서비스 상태를 확인합니다** - 이벤트 허브, 스트림 분석 및 Power BI를 사용하여 진단 데이터를 Azure 서비스에 대한 거의 실시간 인사이트로 변환합니다. 이 솔루션에 대한 자세한 내용은 [스트리밍 데이터에 대한 실시간 분석 대시보드인 스트림 분석 및 Power BI를](../../stream-analytics/stream-analytics-power-bi-dashboard.md) 참조하십시오.

    다음 SQL 코드는 모든 로그 데이터를 Power BI 테이블로 간단히 구문 분석하는 데 사용할 수 있는 샘플 Stream Analytics 쿼리입니다.
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>사전 요구 사항
아직 없는 경우 [이벤트 허브를 만들어야](../../event-hubs/event-hubs-create.md) 합니다. 이 이벤트 허브 네임스페이스를 사용하는 진단 설정이 이미 있는 경우 해당 이벤트 허브가 다시 사용됩니다.

네임스페이스에 대한 공유 액세스 정책은 스트리밍 메커니즘에 있는 권한을 정의합니다. 이벤트 허브로 스트리밍하려면 관리, 보내기 및 수신 권한이 필요합니다. 이벤트 허브 네임스페이스에 대한 구성 탭 아래의 Azure 포털에서 공유 액세스 정책을 만들거나 수정할 수 있습니다.

스트리밍을 포함하도록 진단 설정을 업데이트하려면 해당 이벤트 허브 권한 부여 규칙에 대한 ListKey 권한이 있어야 합니다. 설정을 구성하는 사용자에게 구독 모두에 액세스할 수 있는 적절한 RBAC 액세스 권한이 있다면 Event Hubs 네임스페이스가 로그를 내보내는 구독과 동일한 구독에 위치하지 않아도 됩니다. 구독은 모두 동일한 AAD 테넌트에 위치합니다.

## <a name="create-a-diagnostic-setting"></a>진단 설정 만들기
Azure 리소스에 대한 진단 설정을 만들어 이벤트 허브 및 기타 대상으로 플랫폼 로그를 보냅니다. 자세한 내용은 [Azure에서 로그 및 메트릭을 수집하려면 진단 설정 만들기를](diagnostic-settings.md) 참조하십시오.

## <a name="collect-data-from-compute-resources"></a>컴퓨팅 리소스에서 데이터 수집
진단 설정은 다른 리소스와 마찬가지로 Azure 계산 리소스에 대한 리소스 로그를 수집하지만 게스트 운영 체제 또는 워크로드는 수집하지 않습니다. 이 데이터를 수집하려면 [로그 분석 에이전트를](log-analytics-agent.md)설치합니다. 


## <a name="consuming-log-data-from-event-hubs"></a>이벤트 허브에서 로그 데이터 사용
이벤트 허브의 플랫폼 로그는 다음 표의 요소와 함께 JSON 형식으로 사용됩니다.

| 요소 이름 | 설명 |
| --- | --- |
| 레코드 |이 페이로드에 있는 모든 로그 이벤트의 배열입니다. |
| time |이벤트가 발생한 시간입니다. |
| category |이 이벤트에 대한 로그 범주입니다. |
| resourceId |이 이벤트를 생성한 리소스의 리소스 ID입니다. |
| operationName |작업의 이름입니다. |
| level |(선택 사항) 로그 이벤트 수준을 나타냅니다. |
| properties |이벤트의 속성입니다. 에 설명된 대로 각 Azure [ ]()서비스에 따라 다릅니다. |


다음은 리소스 로그에 대한 이벤트 허브의 샘플 출력 데이터입니다.

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```



## <a name="next-steps"></a>다음 단계

* [리소스 로그에 대해 자세히 알아보기.](platform-logs-overview.md)
* [진단 설정을 만들어 Azure 에서 로그 및 메트릭을 수집합니다.](diagnostic-settings.md)
* [Azure 모니터를 사용 하 고 Azure Active Directory 로그 스트리밍](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)합니다.
* [이벤트 허브를 시작하십시오.](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

