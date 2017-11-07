---
title: " 자동화 Runbook으로 Azure VM 경고 수정 | Microsoft Docs"
description: "이 문서에서는 Azure 가상 컴퓨터 경고를 Azure 자동화 runbook과 통합하고 문제를 자동 수정하는 방법을 보여 줍니다."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 1f7baa7f-7283-4a4f-9385-3f5cd1062c7f
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: csand;magoedte
ms.openlocfilehash: 18cccc88ab74235722e2f4886671fc483ab67da8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---remediate-azure-vm-alerts"></a>Azure 자동화 솔루션 - Azure VM 경고 수정
Azure 자동화 및 Azure 가상 컴퓨터에 자동화 runbook을 실행하도록 가상 컴퓨터(VM) 경고를 구성할 수 있는 새로운 기능이 릴리스되었습니다. 이 새로운 기능을 통해 VM 경고에 대응하여 VM 재시작 또는 중지와 같은 일반 수정 작업을 자동으로 수행할 수 있습니다.

이전에는 경고가 트리거될 때마다 runbook을 실행하기 위해 VM 경고 규칙 생성 중에 [자동화 webhook을 runbook으로 지정](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/) 할 수 있었습니다. 그러나 이렇게 하려면 runbook을 만들고 runbook에 대한 webhook을 만든 후 경고 규칙 생성 중에 webhook을 복사하여 붙여넣어야 합니다. 새 릴리스에서는 경고 규칙을 만드는 동안 목록에서 Runbook을 직접 선택할 수 있고 Runbook을 실행하거나 계정을 쉽게 만들 수 있는 Automation 계정을 선택할 수 있으므로 이 프로세스가 훨씬 쉽습니다.

이 문서에서는 Azure VM 경고를 설정하고 경고가 트리거될 때마다 실행되도록 Automation Runbook을 구성하는 방법이 얼마나 쉬운지를 보여 줍니다. 예제 시나리오에는 VM에 있는 응용 프로그램의 메모리 누수로 인해 메모리 사용량이 임계값을 초과하는 경우 VM을 다시 시작하거나 지난 시간 동안 CPU 사용자 시간이 1% 미만이고 사용 중이지 않으면 VM을 중지하는 것이 포함됩니다. 또한 자동화 계정에서 서비스 주체의 자동화된 생성이 Azure 경고 수정에서 runbook의 사용을 어떻게 간소화하는지도 설명합니다.

## <a name="create-an-alert-on-a-vm"></a>VM에서 경고 만들기
해당 임계값을 충족하는 경우 runbook을 시작하도록 경고를 구성하려면 다음 단계를 수행합니다.

> [!NOTE]
> 이 릴리스에서는 V2 가상 컴퓨터만 지원하며 클래식 VM에 대한 지원은 곧 추가될 예정입니다.  
> 
> 

1. Azure 포털에 로그인하고 **가상 컴퓨터**를 클릭합니다.  
2. 가상 컴퓨터 중 하나를 선택합니다.  
3. VM 화면의 **모니터링** 섹션에서 **경고 규칙**을 클릭합니다.
4. **경고 규칙** 창에서 **경고 추가**를 클릭합니다.

그러면 **경고 규칙 추가** 페이지가 열립니다. 여기서 경고 조건을 구성하고, 누군가에게 전자 메일 보내기, 웹후크를 사용하여 다른 시스템에 경고 전달 및/또는 문제를 수정하기 위한 시도로 Automation Runbook 실행과 같은 옵션 중 하나 또는 모두를 선택할 수 있습니다.

## <a name="configure-a-runbook"></a>Runbook 구성
VM 경고 임계값에 도달할 때 runbook이 실행되도록 구성하려면 **자동화 Runbook**을 선택합니다. **Runbook 구성** 창에서 실행할 Runbook 및 Runbook을 실행할 Automation 계정을 선택할 수 있습니다.

![자동화 runbook 구성 및 새 자동화 계정 만들기](media/automation-azure-vm-alert-integration/ConfigureRunbookNewAccount.png)

> [!NOTE]
> 이 릴리스에서는 서비스에서 제공하는 세 가지 runbook 중에서 선택할 수 있습니다. VM 다시 시작, VM 중지 또는 VM 제거(삭제)입니다.  다른 runbook을 선택하거나 사용자 고유의 runbook 중 하나를 선택하는 기능은 향후 릴리스에서 제공될 예정입니다.
> 
> 

![Runbook 선택](media/automation-azure-vm-alert-integration/RunbooksToChoose.png)

세 가지 사용 가능한 runbook 중 하나를 선택한 후에는 **자동화 계정** 드롭다운 목록이 나타나고 runbook을 실행할 자동화 계정을 선택할 수 있습니다. Runbook은 Azure 구독에 있는 [자동화 계정](automation-security-overview.md) 의 컨텍스트에서 실행되어야 합니다. 사용자가 이미 만든 자동화 계정을 선택하거나 사용자용으로 만들어진 새 자동화 계정을 포함할 수 있습니다.

제공된 runbook은 서비스 주체를 사용하여 Azure에 인증을 수행합니다. 기존 Automation 계정 중 하나에서 Runbook을 실행하도록 선택하면 자동으로 서비스 주체를 만듭니다. 새 Automation 계정을 만들도록 선택하면 자동으로 계정 및 서비스 주체를 만듭니다. 또한 두 경우 모두 Automation 계정에 두 자산, 즉 **AzureRunAsCertificate**라는 인증서 자산과 **AzureRunAsConnection**이라는 연결 자산도 만듭니다. Runbook은 VM에 대한 관리 작업을 수행하기 위해 **AzureRunAsConnection**을 사용하여 Azure를 인증합니다.

> [!NOTE]
> 서비스 주체는 구독 범위에 생성되고 참여자 역할에 할당됩니다. 계정에서 Azure VM을 관리하기 위해 자동화 runbook 실행 권한을 보유하려면 이 역할이 필요합니다.  자동화 계정 생성 및/또는 서비스 주체는 일회성 이벤트입니다. 생성되었으면 다른 Azure VM 경고에 대해 runbook을 실행하는 데 해당 계정을 사용할 수 있습니다.
> 
> 

**확인** 을 클릭하면 경고가 구성되고 새 자동화 계정을 만드는 옵션을 선택하면 서비스 주체와 함께 계정이 만들어집니다.  완료하는 데 몇 초 정도 걸릴 수 있습니다.  

![Runbook 구성 중](media/automation-azure-vm-alert-integration/RunbookBeingConfigured.png)

구성이 완료되면 **경고 규칙 추가** 페이지에서 해당 Runbook의 이름이 표시됩니다.

![Runbook 구성됨](media/automation-azure-vm-alert-integration/RunbookConfigured.png)

**경고 규칙 추가** 페이지에서 **확인**을 클릭합니다.  가상 컴퓨터가 실행 중 상태에 있으면 경고 규칙이 만들어지고 활성화됩니다.

### <a name="enable-or-disable-a-runbook"></a>Runbook 사용 또는 사용하지 않도록 설정
경고에 대해 runbook을 구성한 경우 runbook 구성을 제거하지 않고 사용하지 않도록 설정할 수 있습니다. 이렇게 하면 경고를 실행 상태로 유지하고 일부 경고 규칙을 테스트한 후 나중에 runbook을 다시 사용하도록 설정할 수 있습니다.

## <a name="create-a-runbook-that-works-with-an-azure-alert"></a>Azure 경고를 사용하여 작동하는 Runbook 만들기
Azure 경고 규칙의 일환으로 Runbook을 선택하면 Runbook은 여기에 전달되는 경고 데이터를 관리하는 논리를 포함해야 합니다.  Runbook이 경고 규칙에 구성된 경우 웹후크가 Runbook에 생성됩니다.해당 웹후크는 경고가 트리거될 때마다 Runbook을 시작하는 데 사용됩니다.  Runbook을 시작하는 실제 호출은 웹후크 URL에 대한 HTTP POST 요청입니다. POST 요청의 본문에는 경고와 관련된 유용한 속성을 포함하는 JSON으로 포맷된 개체가 포함되어 있습니다.  아래에서 볼 수 있는 것처럼 경고 데이터는 subscriptionID, resourceGroupName, resourceName, 및 resourceType와 같은 세부 정보를 포함합니다.

### <a name="example-of-alert-data"></a>경고 데이터의 예
```
{
    "WebhookName": "AzureAlertTest",
    "RequestBody": "{
    \"status\":\"Activated\",
    \"context\": {
        \"id\":\"/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/microsoft.insights/alertrules/AlertTest\",
        \"name\":\"AlertTest\",
        \"description\":\"\",
        \"condition\": {
            \"metricName\":\"CPU percentage guest OS\",
            \"metricUnit\":\"Percent\",
            \"metricValue\":\"4.26337916666667\",
            \"threshold\":\"1\",
            \"windowSize\":\"60\",
            \"timeAggregation\":\"Average\",
            \"operator\":\"GreaterThan\"},
        \"subscriptionId\":\<subscriptionID> \",
        \"resourceGroupName\":\"TestResourceGroup\",
        \"timestamp\":\"2016-04-24T23:19:50.1440170Z\",
        \"resourceName\":\"TestVM\",
        \"resourceType\":\"microsoft.compute/virtualmachines\",
        \"resourceRegion\":\"westus\",
        \"resourceId\":\"/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\",
        \"portalLink\":\"https://portal.azure.com/#resource/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\"
        },
    \"properties\":{}
    }",
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "<webhookURL>"
    }
}
```

자동화 웹후크 서비스가 HTTP POST를 수신한 경우 경고 데이터를 추출하고 WebhookData Runbook 입력 매개 변수에서 Runbook에 전달합니다.  다음은 WebhookData 매개 변수를 사용하고 경고 데이터를 추출하고 사용하여 경고를 트리거하는 Azure 리소스를 관리하는 방법을 보여 주는 샘플 Runbook입니다.

### <a name="example-runbook"></a>예제 Runbook
```
#  This runbook restarts an ARM (V2) VM in response to an Azure VM alert.

[OutputType("PSAzureOperationResponse")]

param ( [object] $WebhookData )

if ($WebhookData)
{
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Assure that the alert status is 'Activated' (alert condition went from false to true)
    # and not 'Resolved' (alert condition went from true to false)
    if ($WebhookBody.status -eq "Activated")
    {
        # Get the info needed to identify the VM
        $AlertContext = [object] $WebhookBody.context
        $ResourceName = $AlertContext.resourceName
        $ResourceType = $AlertContext.resourceType
        $ResourceGroupName = $AlertContext.resourceGroupName
        $SubId = $AlertContext.subscriptionId

        # Assure that this is the expected resource type
        Write-Verbose "ResourceType: $ResourceType"
        if ($ResourceType -eq "microsoft.compute/virtualmachines")
        {
            # This is an ARM (V2) VM

            # Authenticate to Azure with service principal and certificate
            $ConnectionAssetName = "AzureRunAsConnection"
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null) {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Restart the VM
            Restart-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName
        } else {
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    } else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $WebhookBody.status)
    }
} else {
    Write-Error "This runbook is meant to be started from an Azure alert only."
}
```

## <a name="summary"></a>요약
Azure VM에서 경고를 구성할 경우 경고가 트리거될 때 수정 작업을 자동으로 수행하도록 이제 자동화 runbook을 쉽게 구성할 수 있습니다. 이 릴리스에서는 경고 시나리오에 따라 runbook에서 VM을 다시 시작, 중지 또는 삭제하도록 선택할 수 있습니다. 이는 경고가 트리거될 때 자동으로 수행되는 작업(알림, 문제 해결, 수정)을 제어하는 시나리오를 시작하는 것일 뿐입니다.

## <a name="next-steps"></a>다음 단계
* 그래픽 Runbook을 시작하려면 [내 첫 번째 그래픽 Runbook](automation-first-runbook-graphical.md)
* PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)
* Runbook 형식, 해당 장점 및 제한 사항에 대해 자세히 알아보려면 [Azure 자동화 Runbook 형식](automation-runbook-types.md)

