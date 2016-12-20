---
title: "Event Hubs로 Azure 진단 로그 스트림 | Microsoft Docs"
description: "이벤트 허브로 Azure 진단 로그 스트림 방법에 대해 알아봅니다."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 42bc4845-c564-4568-b72d-0614591ebd80
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4ff5fb57cba6dea1bee9d2e2d25f6fcf8354ce79


---
# <a name="stream-azure-diagnostic-logs-to-event-hubs"></a>이벤트 허브로 Azure 진단 로그 스트림
포털에서 기본 제공 "Event Hubs로 내보내기" 옵션을 사용하거나 Azure PowerShell Cmdlet 또는 Azure CLI를 통해 진단 로그에서 Service Bus 규칙 ID를 사용하도록 설정하여 **[Azure 진단 로그](monitoring-overview-of-diagnostic-logs.md)**를 거의 실시간으로 응용 프로그램에 스트림할 수 있습니다.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>진단 로그 및 이벤트 허브에서 수행할 수 있는 작업
진단 로그의 스트리밍 기능을 사용할 수 있는 몇 가지 방법은 다음과 같습니다.

* **타사 로깅 및 원격 분석 시스템으로 로그 스트림** – 시간이 지나면서 이벤트 허브 스트리밍은 진단 로그를 타사 SIEM 및 로그 분석 솔루션으로 파이핑하기 위한 메커니즘이 되고 있습니다.
* **"실행 부하 과다 경로" 데이터를 PowerBI로 스트리밍하여 서비스 상태 보기** – 이벤트 허브, 스트림 분석 및 PowerBI를 사용하여 Azure 서비스에서 진단 데이터를 거의 실시간 정보로 간편하게 변환할 수 있습니다. [이 설명서 문서는 이벤트 허브를 설정하고 스트림 분석으로 데이터를 처리하며 출력으로 PowerBI를 사용하는 방법에 대한 훌륭한 개요를 제공합니다](../stream-analytics/stream-analytics-power-bi-dashboard.md). 다음은 진단 로그로 설정하는 방법에 대한 몇 가지 팁입니다.
  
  * 진단 로그의 범주에 대한 이벤트 허브는 포털에서 해당 옵션을 선택하거나 PowerShell을 통해 사용하도록 설정하면 자동으로 생성되므로 "insights-"로 시작하는 이름의 서비스 버스 네임스페이스에서 이벤트 허브를 선택하려고 합니다.
  * 다음은 모든 로그 데이터를 PowerBI 테이블로 간단히 구문 분석하는 데 사용할 수 있는 샘플 스트림 분석 쿼리입니다.

```
SELECT
records.ArrayValue.[Properties you want to track]
INTO
[OutputSourceName – the PowerBI source]
FROM
[InputSourceName] AS e
CROSS APPLY GetArrayElements(e.records) AS records
```

* **사용자 지정 원격 분석 및 로깅 플랫폼 빌드** – 사용자 지정 빌드 원격 분석 플랫폼이 이미 있거나 플랫폼 빌드에 대해 생각하고 있는 경우 이벤트 허브의 확장성 높은 게시-구독 특성을 통해 진단 로그를 유연하게 수집할 수 있습니다. [글로벌 확장 원격 분석 플랫폼에 이벤트 허브 사용에 대해서는 여기 Dan Rosanova의 가이드를 참조하세요.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-diagnostic-logs"></a>진단 로그의 스트리밍 사용
프로그래밍 방식으로나, 포털을 통하거나 [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx)를 사용하여 진단 로그의 스트리밍을 사용하도록 설정할 수 있습니다. 어떤 방법으로든 서비스 버스 네임스페이스를 선택하면 사용할 각 로그 범주에 대한 네임스페이스에 이벤트 허브가 생성됩니다. 진단 **로그 범주** 는 리소스가 수집할 수 있는 로그 형식입니다. 진단 블레이드 아래 Azure 포털에서 특정 리소스에 대해 수집할 로그 범주를 선택할 수 있습니다.

![포털에서 로그 범주](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [!WARNING]
> 계산 리소스(예: VM 또는 서비스 패브릭)에서 진단 로그를 사용 및 스트리밍하려면 [여러 단계 집합을 거쳐야 합니다](../event-hubs/event-hubs-streaming-azure-diags-data.md).
> 
> 

### <a name="via-powershell-cmdlets"></a>PowerShell Cmdlet을 통해
[Azure PowerShell Cmdlet](insights-powershell-samples.md)을 통해 스트리밍을 사용하도록 설정하려면 다음 매개 변수와 함께 `Set-AzureRmDiagnosticSetting` cmdlet을 사용하면 됩니다.

```
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

서비스 버스 규칙 ID는 `{service bus resource ID}/authorizationrules/{key name}` 형식의 문자열입니다. 예를 들면 `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{service bus namespace}/authorizationrules/RootManageSharedAccessKey`입니다.

### <a name="via-azure-cli"></a>Azure CLI를 통해
[Azure CLI](insights-cli-samples.md)를 통해 스트리밍을 사용하도록 설정하려면 다음과 같이 `insights diagnostic set` 명령을 사용하면 됩니다.

```
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

PowerShell Cmdlet에 대해 설명한 것처럼 서비스 버스 규칙 ID와 동일한 형식을 사용합니다.

### <a name="via-azure-portal"></a>Azure 포털을 통해
Azure 포털을 통해 스트리밍을 사용하도록 설정하려면 리소스의 진단 설정으로 이동하고 '이벤트 허브로 내보내기'를 선택합니다.

![포털에서 이벤트 허브로 내보내기](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

구성하려면 기존 서비스 버스 네임스페이스를 선택합니다. 선택한 네임스페이스는 이벤트 허브가 생성(진단 로그를 처음으로 스트리밍하는 경우)되거나 스트림(해당 로그 범주를 이 네임스페이스로 스트리밍하는 리소스가 이미 있는 경우)되는 위치이며 정책은 스트리밍 메커니즘이 포함하는 사용 권한을 정의합니다. 현재, 이벤트 허브로 스트리밍하려면 관리, 읽기 및 보내기 사용 권한이 필요합니다. 클래식 포털에서 서비스 버스 네임스페이스에 대한 "구성" 탭 아래에서 서비스 버스 네임스페이스 공유 액세스 정책을 만들거나 수정할 수 있습니다. 이러한 진단 설정 중 하나를 업데이트하려면 클라이언트에 서비스 버스 권한 부여 규칙에 대한 ListKey 권한이 있어야 합니다.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>이벤트 허브에서 로그 데이터를 사용하려면 어떻게 하나요?
다음은 이벤트 허브의 샘플 출력 데이터입니다.

```
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

| 요소 이름 | 설명 |
| --- | --- |
| 레코드 |이 페이로드에 있는 모든 로그 이벤트의 배열입니다. |
| 실시간 |이벤트가 발생한 시간입니다. |
| 카테고리 |이 이벤트에 대한 로그 범주입니다. |
| resourceId |이 이벤트를 생성한 리소스의 리소스 ID입니다. |
| operationName |작업의 이름입니다. |
| level |선택 사항입니다. 로그 이벤트 수준을 나타냅니다. |
| properties |이벤트의 속성입니다. |

이벤트 허브로 스트리밍을 지원하는 모든 리소스 공급자의 목록을 [여기](monitoring-overview-of-diagnostic-logs.md)에서 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure 진단 로그에 대해 자세히 알아보기](monitoring-overview-of-diagnostic-logs.md)
* [이벤트 허브 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)




<!--HONumber=Nov16_HO3-->


