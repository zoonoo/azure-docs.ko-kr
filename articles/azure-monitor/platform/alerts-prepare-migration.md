---
title: 논리 앱 및 runbook을 업데이트하여 Azure Monitor 클래식 경고 마이그레이션 준비
description: 자발적 마이그레이션을 준비하기 위해 웹후크, 논리 앱 및 Runbook을 수정하는 방법을 알아봅니다.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 9219e105acb98424939030af76b526d475585619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665595"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>클래식 경고 규칙 마이그레이션을 위한 논리 앱 및 Runbook 준비

[이전에 발표된](monitoring-classic-retirement.md)대로 Azure Monitor의 클래식 경고는 2019년 9월에 사용 중지됩니다(원래 2019년 7월). Azure 포털에서 클래식 경고 규칙을 사용하고 마이그레이션을 트리거하려는 고객에게 마이그레이션 도구를 사용할 수 있습니다.

> [!NOTE]
> 마이그레이션 도구의 롤아웃 이 지연으로 인해 클래식 경고 마이그레이션의 사용 중지 날짜가 원래 발표된 날짜인 2019년 6월 30일부터 2019년 8월 31일로 연장되었습니다.

클래식 경고 규칙을 새 경고 규칙으로 자발적으로 마이그레이션하도록 선택한 경우 두 시스템 간에 몇 가지 차이점이 있습니다. 이 문서에서는 이러한 차이점과 변경에 대비할 수 있는 방법에 대해 설명합니다.

## <a name="api-changes"></a>API 변경

클래식 경고 규칙()을`microsoft.insights/alertrules`만들고 관리하는 API는 새 메트릭 경고()를`microsoft.insights/metricalerts`만들고 관리하는 API와 다릅니다. 오늘 클래식 경고 규칙을 프로그래밍 방식으로 만들고 관리하는 경우 배포 스크립트를 업데이트하여 새 API로 작업합니다.

다음 표는 클래식 및 새 경고모두에 대한 프로그래밍 인터페이스에 대한 참조입니다.

|         |클래식 경고  |새 메트릭 경고 |
|---------|---------|---------|
|REST API     | [마이크로 소프트.인사이트 / 경고 규칙](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [마이크로 소프트.인사이트 / 메트릭 경고](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az 모니터 경고](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az 모니터 메트릭 경고](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [참조](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [참조](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure Resource Manager 템플릿 | [클래식 경고용](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[새 메트릭 경고의 경우](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>알림 페이로드 변경

알림 페이로드 형식은 [클래식 경고 규칙과](alerts-webhooks.md) [새 메트릭 경고](alerts-metric-near-real-time.md#payload-schema)간에 약간 다릅니다. 클래식 경고 규칙에 의해 트리거되는 웹후크, 논리 앱 또는 Runbook 작업이 있는 경우 해당 알림 끝점을 업데이트하여 새 메트릭 경고의 페이로드 형식을 수락해야 합니다.

다음 표를 사용하여 웹후크 페이로드 필드를 클래식 형식에서 새 형식으로 매핑합니다.

|  |클래식 경고  |새 메트릭 경고 |
|---------|---------|---------|
|경고가 활성화되었거나 해결되었습니까?    | **상태**       | **데이터 상태** |
|경고에 대한 컨텍스트 정보     | **context**        | **데이터.컨텍스트**        |
|경고가 활성화또는 해결된 타임스탬프     | **컨텍스트.타임스탬프**       | **데이터.컨텍스트.타임스탬프**        |
| 경고 규칙 ID | **context.id** | **data.context.id** |
| 경고 규칙 이름 | **context.name** | **data.context.name** |
| 경고 규칙에 대한 설명 | **컨텍스트 설명** | **data.context.설명** |
| 경고 규칙 조건 | **컨텍스트.조건** | **데이터.컨텍스트.조건** |
| 메트릭 이름 | **컨텍스트.조건.메트릭이름** | **데이터.컨텍스트.조건.allOf[0].metricName** |
| 시간 집계(평가 기간 동안 메트릭이 집계되는 방식)| **컨텍스트.조건.시간Aggregation** | **컨텍스트.조건.시간Aggregation** |
| 평가 기간 | **컨텍스트.조건.창크기** | **데이터.컨텍스트.조건.창크기** |
| 연산자(집계된 메트릭 값을 임계값과 비교하는 방법) | **컨텍스트.조건.연산자** | **데이터.컨텍스트.조건.연산자** |
| 임계값 | **컨텍스트.조건.임계값** | **데이터.컨텍스트.조건.allOf[0].임계값** |
| 메트릭 값 | **컨텍스트.조건.메트릭값** | **데이터.컨텍스트.조건.allOf[0].metricValue** |
| 구독 ID | **컨텍스트.구독ID** | **데이터.컨텍스트.구독ID** |
| 영향을 받는 리소스의 리소스 그룹 | **컨텍스트.리소스 그룹** | **데이터.컨텍스트.리소스 그룹** |
| 영향을 받는 리소스의 이름 | **컨텍스트.리소스이름** | **데이터.컨텍스트.리소스이름** |
| 영향을 받는 리소스의 유형 | **컨텍스트.리소스유형** | **데이터.컨텍스트.리소스유형** |
| 영향을 받는 리소스의 리소스 ID | **컨텍스트.resourceId** | **데이터.컨텍스트.resourceId** |
| 포털 리소스 요약 페이지로 직접 연결 | **컨텍스트.포털링크** | **데이터.컨텍스트.포털링크** |
| 웹후크 또는 논리 앱에 전달할 사용자 지정 페이로드 필드 | **속성** | **데이터.속성** |

보시다시피 페이로드는 비슷합니다. 다음 섹션에서는 다음을 제공합니다.

- 새 형식으로 작동하도록 논리 앱을 수정하는 세부 정보입니다.
- 새 경고에 대한 알림 페이로드를 구문 분석하는 Runbook 예제입니다.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>메트릭 경고 알림을 수신하도록 논리 앱을 수정합니다.

클래식 경고가 있는 논리 앱을 사용하는 경우 새 메트릭 경고 페이로드를 구문 분석하려면 논리 앱 코드를 수정해야 합니다. 다음 단계를 수행하세요.

1. 새 논리 앱을 만듭니다.

1. 템플릿 "Azure 모니터 - 메트릭 경고 처리기"를 사용합니다. 이 템플릿에는 적절한 스키마가 정의된 **HTTP 요청** 트리거가 있습니다.

    ![논리 앱 템플릿](media/alerts-migration/logic-app-template.png "메트릭 경고 템플릿")

1. 처리 논리를 호스트하는 작업을 추가합니다.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>메트릭 경고 알림을 받는 자동화 Runbook 사용

다음 예제에서는 Runbook에서 사용할 PowerShell 코드를 제공합니다. 이 코드는 클래식 메트릭 경고 규칙과 새 메트릭 경고 규칙 모두에 대한 페이로드를 구문 분석할 수 있습니다.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

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

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
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

경고가 트리거될 때 가상 컴퓨터를 중지하는 Runbook의 전체 예는 [Azure Automation 설명서를](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)참조하십시오.

## <a name="partner-integration-via-webhooks"></a>웹후크를 통한 파트너 통합

클래식 [경고와 통합되는](https://docs.microsoft.com/azure/azure-monitor/platform/partners) 대부분의 파트너는 이미 통합을 통해 최신 메트릭 경고를 지원합니다. 새 메트릭 경고와 함께 이미 작동하는 알려진 통합은 다음과 같습니다.

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [시그널4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

여기에 나열되지 않은 파트너 통합을 사용하는 경우 통합 공급자에게 통합이 새 메트릭 경고와 함께 작동하는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [마이그레이션 도구 사용 방법](alerts-using-migration-tool.md)
- [마이그레이션 도구의 작동 방식 이해](alerts-understand-migration.md)
