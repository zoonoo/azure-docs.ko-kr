---
title: "Automation Runbook에서 Azure 경고에 응답 | Microsoft Docs"
description: "Azure 경고가 발생할 경우 실행할 Runbook을 트리거하는 방법을 알아봅니다."
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 01/11/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 486a3c80c9d6c19ca0a7ba7d4e3cdcde927053b9
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="respond-to-azure-alerts-with-an-automation-runbook"></a>Automation Runbook으로 Azure 경고에 응답

[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json)는 대부분의 Microsoft Azure 서비스에 대한 기본 수준의 메트릭과 로그를 제공합니다. [작업 그룹](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json)을 사용하거나 경고에 따라 작업을 자동화하기 위한 클래식 경고를 통해 Azure Automation Runbook을 호출할 수 있습니다. 이 문서에서는 이러한 경고를 기반으로 Runbook을 구성하고 실행하는 방법을 보여 줍니다.

## <a name="alert-types"></a>경고 유형

Runbook은 세 가지 경고 유형 모두에 대해 지원되는 작업입니다. 경고가 Runbook을 호출할 때 실제 호출은 웹후크에 대한 HTTP POST 요청입니다. POST 요청의 본문에는 경고와 관련된 유용한 속성을 포함하는 JSON으로 포맷된 개체가 포함되어 있습니다. 다음 표에는 각 경고 유형에 대한 페이로드 스키마 링크가 포함되어 있습니다.

|경고  |설명|페이로드 스키마  |
|---------|---------|---------|
|[클래식 메트릭 경고](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |플랫폼 수준 메트릭이 특정 조건에 부합하면(예: VM의 CPU %가 지난 5분 동안 90%를 넘음) 알림을 받습니다.| [페이로드 스키마](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[활동 로그 경고](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Azure 활동 로그에서 새 이벤트가 특정 조건(예: myProductionResourceGroup에서 "VM 삭제" 작업이 발생했거나 상태가 "활성"인 새 서비스 상태 이벤트가 나타난 경우)에 부합하는 경우 알림을 받습니다.| [페이로드 스키마](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[근 실시간 메트릭 경고 만들기](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |하나 이상의 플랫폼 수준 메트릭이 지정된 조건을 충족하는 경우 알림을 메트릭 경고보다 먼저 수신합니다.(예를 들어, 지난 5분 동안 VM의 CPU 비율이 90보다 크고 네트워크 입력이 500MB보다 큽니다.)| [페이로드 스키마](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

각 경고에서 제공하는 데이터는 서로 다르기 때문에 각 경고를 다르게 처리해야 합니다. 다음 섹션에서는 이러한 다양한 유형의 경고를 처리하는 Runbook을 만드는 방법에 대해 배웁니다.

## <a name="create-a-runbook-to-handle-alerts"></a>경고를 처리할 Runbook 만들기

경고와 함께 자동화를 사용하려면 Runbook에 전달되는 경고 JSON 페이로드를 관리하는 논리가 있는 Runbook이 필요합니다. 다음 Runbook 예제는 Azure 경고에서 호출되어야 합니다. 앞 섹션에서 설명한 것처럼 각 경고 유형은 서로 다른 스키마를 사용합니다. 스크립트는 `WebhookData` Runbook 입력 매개 변수의 웹후크 데이터를 경고에서 가져와서 JSON 페이로드를 평가하여 사용된 경고 유형을 확인합니다. 다음 예제는 VM의 경고에 사용됩니다. 페이로드에서 VM 데이터를 검색하고 해당 정보를 사용하여 VM을 중지합니다. Runbook이 실행되는 Automation 계정에서 연결을 설정해야 합니다.

Runbook은 VM에 대한 관리 작업을 수행하기 위해 **AzureRunAsConnection**을 [계정으로 실행](automation-create-runas-account.md)하여 Azure를 인증합니다.

다음 PowerShell 스크립트는 여러 리소스에서 사용하도록 변경할 수 있습니다.

다음 예제를 수행하고 샘플 PowerShell로 **Stop-AzureVmInResponsetoVMAlert**이라는 Runbook을 만듭니다.

1. Automation 계정을 엽니다.
1. **프로세스 자동화** 아래에서 **Runbook**을 클릭합니다. Runbook 목록이 표시됩니다.
1. 목록 위쪽의 **Runbook 추가** 단추를 클릭합니다. **Runbook 추가 페이지**에서 **빨리 만들기**를 선택합니다.
1. Runbook **이름**으로 "Stop-AzureVmInResponsetoVMAlert"을 입력하고, **Runbook 형식**으로 **PowerShell**을 선택합니다. **만들기**를 클릭합니다.
1. 다음 PowerShell 예제를 편집 창에 복사합니다. **게시**를 클릭하여 Runbook을 저장하고 게시합니다.

```powershell-interactive
<#
.SYNOPSIS
  This runbook will stop a resource management VM in response to an Azure alert trigger.

.DESCRIPTION
  This runbook will stop an resource management VM in response to an Azure alert trigger.
  Input is alert data with information needed to identify which VM to stop.
  
  DEPENDENCIES
  - The runbook must be called from an Azure alert via a webhook.
  
  REQUIRED AUTOMATION ASSETS
  - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
  - An Automation certificate asset called "AzureRunAsCertificate".

.PARAMETER WebhookData
   Optional (user does not need to enter anything, but the service will always pass an object)
   This is the data that is sent in the webhook that is triggered from the alert.

.NOTES
   AUTHOR: Azure Automation Team
   LASTEDIT: 2017-11-22
#>

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Get the info needed to identify the VM (depends on the payload schema)
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {
        # This is the near-real-time Metric Alert schema
        $AlertContext = [object] ($WebhookBody.data).context
        $ResourceName = $AlertContext.resourceName
        $status = ($WebhookBody.data).status
    }
    elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
        # This is the Activity Log Alert schema
        $AlertContext = [object] (($WebhookBody.data).context).activityLog
        $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
        $status = ($WebhookBody.data).status
    }
    elseif ($schemaId -eq $null) {
        # This is the original Metric Alert schema
        $AlertContext = [object] $WebhookBody.context
        $ResourceName = $AlertContext.resourceName
        $status = $WebhookBody.status
    }
    else {
        # Schema not supported
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    Write-Verbose "status: $status" -Verbose
    if ($status -eq "Activated")
    {
        $ResourceType = $AlertContext.resourceType
        $ResourceGroupName = $AlertContext.resourceGroupName
        $SubId = $AlertContext.subscriptionId
        Write-Verbose "resourceType: $ResourceType" -Verbose
        Write-Verbose "resourceName: $ResourceName" -Verbose
        Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
        Write-Verbose "subscriptionId: $SubId" -Verbose

        # Do work only if this is a resource management VM
        if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
        {
            # This is the VM
            Write-Verbose "This is a resource management VM." -Verbose

            # Authenticate to Azure with service principal and certificate and set subscription
            Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
            $ConnectionAssetName = "AzureRunAsConnection"
            Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Verbose "Authenticating to Azure with service principal." -Verbose
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Write-Verbose "Setting subscription to work against: $SubId" -Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Stop the VM
            Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
            Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
            # [OutputType(PSAzureOperationResponse")]
        }
        else {
            # ResourceType not supported
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    }
    else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
    }
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="create-an-action-group"></a>작업 그룹 만들기

작업 그룹은 경고에 따라 수행되는 작업 컬렉션입니다. Runbook은 작업 그룹에서 사용할 수 있는 많은 작업 중 하나일 뿐입니다.

1. 포털에서 **모니터**를 선택합니다.

1. **설정**에서 **작업 그룹**을 선택합니다.

1. **작업 그룹 추가**를 선택하고 필드를 입력합니다.

1. 작업 그룹 이름 상자에 이름을 입력하고 짧은 이름 상자에 이름을 입력합니다. 약식 이름은 이 그룹을 사용하여 알림을 보내는 경우 전체 작업 그룹 이름 대신 사용됩니다.

1. 구독 상자가 현재 구독으로 자동으로 채워집니다. 이 구독은 작업 그룹이 저장되는 위치입니다.
작업 그룹이 저장되는 리소스 그룹을 선택합니다.

이 예제에서는 두 가지 작업, Runbook 작업과 알림 작업을 만듭니다.

### <a name="runbook-action"></a>Runbook 작업

다음 단계에서는 작업 그룹 내에 Runbook 작업을 만듭니다.

1. **작업**에서 작업 이름을 지정합니다.

1. **작업 유형**으로 **Automation Runbook**을 선택합니다.

1. **세부 정보**에서 **세부 정보 편집**을 선택합니다.

1. **Runbook 구성** 페이지의 **Runbook 소스**에서 **사용자**를 선택합니다.

1. **구독** 및 **Automation 계정**을 선택하고 마지막으로 이전 단계에서 만든 "Stop-AzureVmInResponsetoVMAlert" Runbook을 선택합니다.

1. 완료하면 **확인**을 클릭합니다.

### <a name="notification-action"></a>알림 동작

다음 단계에서는 작업 그룹 내에 알림 작업을 만듭니다.

1. **작업**에서 작업 이름을 지정합니다.

1. **작업 유형**은 **메일**을 선택합니다.

1. **세부 정보**에서 **세부 정보 편집**을 선택합니다.

1. **메일** 페이지에서 알리려고 하는 메일 주소를 입력하고 **확인**을 클릭합니다. 메일 주소와 Runbook을 작업으로 추가하면 Runbook이 시작될 때 알려주기 때문에 도움이 됩니다. 작업 그룹은 다음과 같은 이미지여야 합니다.

   ![작업 그룹 페이지 추가](./media/automation-create-alert-triggered-runbook/add-action-group.png)

1. **확인**을 클릭하여 작업 그룹을 만듭니다.

작업 그룹이 만들어지면 [활동 로그 경고](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) 또는 [근 실시간 경고](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal)를 만들고 만든 작업 그룹을 사용할 수 있습니다.

## <a name="classic-alert"></a>클래식 경고

클래식 경고는 메트릭을 기반으로 하기 때문에 작업 그룹을 사용하지 않지만 이를 기반으로 하는 Runbook 작업이 있습니다. 클래식 경고를 만들려면 다음 단계를 사용합니다.

1. **+ 메트릭 경고 추가**를 선택합니다.

1. 메트릭 경고 이름을 'myVMCPUAlert'로 지정하고 경고에 대한 간략한 설명을 제공합니다.

1. 메트릭 경고에 대한 조건을 '다음 값보다 큼'으로 설정하고 임계값을 '10'으로, 기간을 'Over the last 5 minutes(지난 5분 동안)'로 설정합니다.

1. **작업 수행**에서 **이 경고에서 Runbook 실행**을 선택합니다.

1. **Runbook 구성** 페이지에서 **Runbook 소스**에 대해 **사용자**를 선택합니다. 자동화 계정을 선택하고 **Stop-AzureVmInResponsetoVMAlert** Runbook을 선택합니다. **확인**을 클릭한 다음 다시 **확인**을 클릭하여 경고 규칙을 저장합니다.

## <a name="next-steps"></a>다음 단계

* 웹후크에서 Automation Runbook 시작에 대한 자세한 내용은 [웹후크에서 Runbook 시작](automation-webhooks.md)을 참조하세요.
* Runbook을 시작하는 다양한 방법에 대한 자세한 내용은 [Runbook 시작](automation-starting-a-runbook.md)을 참조하세요.
* 활동 로그 경고를 만드는 방법을 알아보려면 [활동 로그 경고 만들기](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)를 참조하세요.
* 근 실시간 경고를 만드는 방법을 보려면 [Azure Portal에서 경고 규칙 만들기](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal)를 방문하세요.