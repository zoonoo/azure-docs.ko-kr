---
title: PowerShell을 사용하여 Azure 서비스에 대한 클래식 경고 만들기| Microsoft Docs
description: 사용자가 지정한 조건이 충족되면 이메일 또는 알림을 트리거하거나 웹 사이트 URL(webhook) 또는 자동화를 호출합니다.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: b08a8f66add45d64085ac05605fe3c7d7f91b705
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286202"
---
# <a name="use-powershell-to-create-alerts-for-azure-services"></a>PowerShell을 사용하여 Azure 서비스에 대한 경고 만들기
> [!div class="op_single_selector"]
> * [포털](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

> [!NOTE]
> 이 문서에서는 기존 클래식 메트릭 경고를 만드는 방법을 설명합니다. Azure Monitor는 이제 [더 새롭고 더 나은 메트릭 경고](monitoring-near-real-time-metric-alerts.md)를 지원합니다. 이러한 경고는 여러 메트릭을 모니터링하고 차원 메트릭에 대한 경고를 허용할 수 있습니다. 더 새로운 메트릭 경고를 위한 PowerShell 지원이 곧 제공됩니다.
>
>

이 문서에서는 PowerShell을 사용하여 Azure 클래식 메트릭 경고를 설정하는 방법을 보여줍니다.  

Azure 서비스에 대한 메트릭을 기반으로 경고를 받을 수도 있고, Azure에서 발생하는 이벤트에 대한 경고를 받을 수도 있습니다.

* **메트릭 값**: 이 경고는 특정 메트릭의 값이 어느 방향으로든 사용자가 할당한 임계값을 초과했을 때 트리거됩니다. 즉, 조건이 처음으로 충족될 때 그리고 해당 조건이 더 이상 충족되지 않을 때 트리거됩니다.    
* **활동 로그 이벤트**: *모든* 이벤트에 대해 또는 특정 이벤트가 발생할 때 경고를 트리거할 수 있습니다. 활동 로그 경고에 대한 자세한 내용은 [활동 로그 경고 만들기(클래식)](monitoring-activity-log-alerts.md)를 참조하세요.

트리거되면 다음 작업을 수행하도록 클래식 메트릭 경고를 구성할 수 있습니다.

* 서비스 관리자 및 공동 관리자에게 메일 알림을 보냅니다.
* 사용자가 지정한 추가 이메일 주소로 이메일을 보냅니다.
* Webhook를 호출합니다.
* Azure Runbook 실행을 시작합니다(현재는 Azure Portal에서만 가능).

다음 위치를 사용하여 경고 규칙을 구성하고 관련 정보를 가져올 수 있습니다. 

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure CLI(명령줄 인터페이스)](insights-alerts-command-line-interface.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

언제든지 필요한 PowerShell 명령 뒤에 ```Get-Help```를 입력하면 추가 정보를 얻을 수 있습니다.

## <a name="create-alert-rules-in-powershell"></a>PowerShell에서 경고 규칙 만들기
1. Azure에 로그인합니다.   

    ```PowerShell
    Connect-AzureRmAccount

    ```
2. 사용 가능한 구독 목록을 가져옵니다. 현재 올바른 구독을 사용 중인지 확인합니다. 그렇지 않은 경우 `Get-AzureRmSubscription`의 출력을 사용하여 올바른 구독으로 이동합니다.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. 다음 명령을 사용하여 리소스 그룹에 대한 기존 규칙을 나열합니다.

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. 규칙을 만들려면 먼저 몇 가지 중요한 정보가 필요합니다.

    - 경고를 설정할 리소스의 **리소스 ID**입니다.
    - 해당 리소스에 사용 가능한 **메트릭 정의**입니다.

     리소스 ID를 가져오는 한 가지 방법은 Azure Portal을 사용하는 것입니다. 리소스를 이미 만든 것으로 가정하고, 포털에서 해당 리소스를 선택합니다. 그 다음 블레이드의 **설정** 섹션에서 **속성**을 선택합니다. **리소스 ID**는 다음 블레이드의 필드입니다. 
     
     [Azure Resource Explorer](https://resources.azure.com/)를 사용하여 리소스 ID를 가져올 수도 있습니다.

     다음은 웹앱의 리소스 ID 예제입니다.

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     `Get-AzureRmMetricDefinition`을 사용하여 특정 리소스에 대한 모든 메트릭 정의를 볼 수 있습니다.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     다음은 해당 메트릭에 대한 메트릭 이름 및 단위를 사용하여 테이블을 생성하는 예제입니다.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     Get-AzureRmMetricDefinition에 사용 가능한 모든 옵션 목록을 가져오려면 `Get-Help Get-AzureRmMetricDefinition -Detailed` 명령을 실행합니다.
5. 다음 예제는 웹 사이트 리소스에 대한 경고를 설정합니다. 이 경고는 어느 트래픽이나 5분 동안 계속 수신되면 항상 트리거되며, 트래픽이 5분 동안 수신되지 않으면 다시 트리거됩니다.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. 경고가 트리거될 때 webhook를 만들거나 이메일을 보내려면 먼저 이메일 또는 webhook를 만듭니다. 그런 다음, 즉시 다음 예제처럼 -Actions 태그를 사용하여 이후의 규칙을 만듭니다. webhook 또는 이메일을 이미 생성된 규칙과 연결할 수 없습니다.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. 개별 규칙을 검토하여 경고가 올바르게 생성되었는지 확인합니다.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. 경고를 삭제합니다. 이 명령은 이 문서의 앞부분에서 만든 규칙을 삭제합니다.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>다음 단계
* 수집하고 모니터링할 수 있는 정보 종류를 포함하여 [Azure 모니터링 개요를 확인](monitoring-overview.md)합니다.
* [경고에서 웹후크 구성](insights-webhooks-alerts.md)을 알아봅니다.
* [활동 로그 이벤트에 대한 경고 구성](monitoring-activity-log-alerts.md) 방법을 알아봅니다.
* [Azure Automation Runbook](../automation/automation-starting-a-runbook.md)에 대해 자세히 알아봅니다.
* 서비스의 상세 고빈도 메트릭을 수집하기 위한 [진단 로그 수집](monitoring-overview-of-diagnostic-logs.md) 의 개요를 살펴봅니다.
* 서비스를 사용 가능하며 응답할 수 있는 상태로 유지하기 위한 [메트릭 수집](insights-how-to-customize-monitoring.md) 의 개요를 살펴봅니다.
