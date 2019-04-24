---
title: 논리 앱 및 runbook을 업데이트 하 여 Azure Monitor 클래식 경고 마이그레이션 준비
description: 웹 후크, 논리 앱과 자발적인 마이그레이션을 준비 하는 runbook을 수정 하는 방법에 알아봅니다.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 3c47404826d5055d4a82d4842523f790fb11f000
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346883"
---
# <a name="prepare-your-logic-apps-and-run-books-for-classic-alert-rules-migration"></a>논리 앱을 준비 하 고 클래식 경고 규칙 마이그레이션에 대 한 온라인 설명서를 실행 합니다.

로 [발표](monitoring-classic-retirement.md), Azure Monitor에서 클래식 경고 2019 년 7 월에에서 사용 중지 됩니다. 마이그레이션 도구 마이그레이션을 자발적으로 트리거를 Azure portal에서 사용할 수 있으며 클래식 경고 규칙을 사용 하는 고객에 게 배포 됩니다.

새 경고 규칙에 클래식 경고 규칙을 자발적으로 마이그레이션하도록 선택 하면 일부의 차이점이 알고 있어야 하는 두 시스템 간의 합니다. 이 문서에서는 두 시스템 차이점과 변경에 대 한 준비 하는 방법을 안내 합니다.

## <a name="api-changes"></a>API 변경

클래식 경고 규칙 만들기/관리 하는 데 Api (`microsoft.insights/alertrules`)은 새로운 메트릭 경고 만들기/관리 하는 데 사용 되는 Api와 다릅니다 (`microsoft.insights/metricalerts`). 관리 하는 경우 프로그래밍 방식으로 만들기/클래식 경고 규칙 현재, 새 Api를 사용 하려면 배포 스크립트를 업데이트 합니다.

다음 표에서 클래식 및 새 경고에 대 한 프로그래밍 인터페이스에 대 한 참조를 제공합니다.

|         |클래식 경고  |새로운 메트릭 경고 |
|---------|---------|---------|
|REST API     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az monitor 메트릭 경고](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [참조](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Azure Resource Manager 템플릿 | [클래식 경고에 대 한](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[새로운 메트릭 경고에 대 한](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>알림 페이로드가 변경

알림 페이로드 형식 간에 약간 다릅니다 [클래식 경고 규칙](alerts-webhooks.md) 하 고 [새 메트릭 경고](alerts-metric-near-real-time.md#payload-schema)합니다. 모든 웹 후크, 논리 앱에 있는 경우 또는 클래식 경고 규칙에 의해 트리거되는 runbook 작업에는 새 메트릭 경고 페이로드 형식을 허용 하도록 이러한 알림 끝점을 업데이트 해야 합니다.

다음 표에서 클래식 경고 규칙이 웹 후크 페이로드 및 새로운 메트릭 경고 웹 후크 페이로드 사이 필드를 매핑할 사용할 수 있습니다.

|  |클래식 경고  |새로운 메트릭 경고 |
|---------|---------|---------|
|경고 활성화 되었거나 해결     | status       | data.status |
|경고에 대 한 컨텍스트 정보     | context        | data.context        |
|타임 스탬프는 경고가 활성화 되었거나 해결      | context.timestamp       | data.context.timestamp        |
| 경고 규칙 ID | context.id | data.context.id |
| 경고 규칙 이름 | context.name | data.context.name |
| 경고 규칙의 설명 | context.description | data.context.description |
| 경고 규칙 조건 | context.condition | data.context.condition|
| 메트릭 이름 | context.condition.metricName| data.context.condition.allOf[0].metricName|
| 시간 집계 (메트릭이 집계 되는 방식을 평가 창 위에)|data.context.condition.timeAggregation|data.context.condition.timeAggregation|
| 평가 기간 | context.condition.windowSize | data.context.condition.windowSize|
| 연산자 (집계 된 메트릭 값을 비교 방법을 임계값에 대해) | context.condition.operator | data.context.condition.operator|
| 임계값 | context.condition.threshold| data.context.condition.allOf[0].threshold|
| 메트릭 값 | context.condition.metricValue | data.context.condition.allOf[0].metricValue|
| 구독 ID | context.subscriptionId | data.context.subscriptionId|
| 영향을 받는 리소스의 리소스 그룹 | context.resourceGroup | data.context.resourceGroup|
| 영향을 받는 리소스의 이름 | context.resourceName | data.context.resourceName |
| 영향을 받는 리소스의 형식 | context.resourceType | data.context.resourceType |
|  영향을 받는 리소스의 리소스 ID | context.resourceId | data.context.resourceId |
| 포털 리소스 요약 페이지에 직접 링크 | context.portalLink | data.context.portalLink|
| 웹 후크 또는 논리 앱에 전달할 사용자 지정 페이로드 필드 | properties |data.properties |

알 수 있듯이 두 페이로드는 유사 합니다. 다음 섹션에는 샘플 논리 앱에 대 한 세부 정보 및 새 경고에 대 한 알림 페이로드를 구문 분석 하는 샘플 runbook

## <a name="using-a-logic-app-that-receives-a-metric-alert-notification"></a>메트릭 경고 알림을 수신 하는 논리 앱을 사용 하 여

클래식 경고를 사용 하 여 논리 앱을 사용 중인 경우에 새 메트릭 경고 페이로드를 구문 분석 논리 앱을 수정 하는 것이 해야 합니다.

1. 새 논리 앱을 만듭니다.

2. "Azure 모니터-메트릭 경고 처리기" 템플릿을 사용 합니다. 이 서식 파일에는 **HTTP 요청** 정의 된 적절 한 스키마를 사용 하 여 트리거

    ![논리 앱 템플릿](media/alerts-migration/logic-app-template.png "메트릭 알림 템플릿")

3. 처리 논리를 호스트 하는 작업을 추가 합니다.

## <a name="using-an-automation-runbook-that-receives-a-metric-alert-notification"></a>메트릭 경고 알림을 수신 하는 automation runbook을 사용 하 여

다음 샘플 클래식 메트릭 경고 규칙와 새로운 메트릭 경고 규칙에 대 한 페이로드를 구문 분석할 수 있는 runbook에서 사용할 수 있는 PowerShell 코드를 제공 합니다.

```PS
## Sample PowerShell code to be used in a runbook to handle parsing of both classic and new metric alerts

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Identify if the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new Metric Alert schema
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic Metric Alert schema
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is not either a classic metric alert or a new metric alert
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    #parse fields related to resource affected
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Runbook에는 경고가 트리거될 때 VM을 중지 하는 전체 예제를 보려면 [Azure Automation 설명서](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)합니다.

## <a name="partner-integration-via-webhooks"></a>웹 후크를 통해 파트너 통합

대부분의 [클래식 경고와 통합 되는 파트너](https://docs.microsoft.com/azure/azure-monitor/platform/partners) 이미 해당 통합을 통해 최신 메트릭 경고를 지원 합니다. 새로운 메트릭 경고를 이미 사용 하는 알려진된 통합은 다음과 같습니다.

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

여기에 나열 되지 않은 파트너 통합을 사용 하는, 하는 경우 통합 공급자를 사용 하 여 작동 하는지 확인 통합 새로운 메트릭 경고를 사용 하 여 합니다.

## <a name="next-steps"></a>다음 단계

- [마이그레이션 도구를 사용 하는 방법](alerts-using-migration-tool.md)
- [마이그레이션 도구 작동 원리를 이해합니다](alerts-understand-migration.md)
