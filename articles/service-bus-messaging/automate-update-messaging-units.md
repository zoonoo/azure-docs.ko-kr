---
title: Azure Service Bus - 메시징 단위 자동 업데이트
description: 이 문서에서는 Azure Automation Runbook을 사용하여 Service Bus 네임스페이스의 메시징 단위를 자동으로 업데이트하는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 52f5b13b482739bfa56ff606f684fd5a9c7d3b6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341491"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Azure Service Bus 네임스페이스의 메시징 단위 자동 업데이트 
이 문서에서는 리소스(CPU 또는 메모리) 사용량에 따라 Service Bus 네임스페이스의 [메시징 단위](service-bus-premium-messaging.md)를 자동으로 업데이트하는 방법을 보여 줍니다. 

이 문서의 예제에서는 네임스페이스의 CPU 사용량이 75%를 초과하는 경우 Service Bus 네임스페이스에 대한 메시징 단위를 늘리는 방법을 보여 줍니다. 대략적인 단계는 다음과 같습니다.

1. Service Bus 네임스페이스에 대한 메시징 단위를 확장(늘리기)하는 PowerShell 스크립트를 사용하여 Azure Automation Runbook을 만듭니다. 
2. 네임스페이스 CPU 사용량이 75%를 초과하는 경우 PowerShell 스크립트를 호출하는 Service Bus 네임스페이스에 대한 CPU 사용량 경고를 만듭니다. 

> [!IMPORTANT]
> 이 문서는 Azure Service Bus의 **프리미엄** 계층에만 적용됩니다. 


## <a name="create-a-service-bus-namespace"></a>Service Bus 네임스페이스 만들기
프리미엄 계층 Service Bus 네임스페이스를 만듭니다. [Azure Portal에서 네임스페이스 만들기](service-bus-quickstart-portal.md#create-a-namespace-in-the-azure-portal) 문서의 단계에 따라 네임스페이스를 만듭니다. 

## <a name="create-an-azure-automation-account"></a>Azure Automation 계정 만들기
[Azure Automation 계정 만들기](../automation/automation-quickstart-create-account.md) 문서의 지침에 따라 Azure Automation 계정을 만듭니다. 

## <a name="import-azservice-module-from-gallery"></a>갤러리에서 Az. Service 모듈 가져오기
갤러리에서 `Az.Accounts` 및 `Az.ServiceBus` 모듈을 자동화 계정으로 가져옵니다. `Az.ServiceBus` 모듈은 `Az.Accounts` 모듈에 종속되므로 먼저 설치해야 합니다. 

단계별 지침은 [모듈 갤러리에서 모듈 가져오기](../automation/automation-runbook-gallery.md#import-a-module-from-the-module-gallery-with-the-azure-portal)를 참조하세요.

## <a name="create-and-publish-a-powershell-runbook"></a>PowerShell Runbook 만들기 및 게시

1. [PowerShell Runbook 만들기](../automation/automation-quickstart-create-runbook.md) 문서의 지침에 따라 PowerShell Runbook을 만듭니다. 

    Service Bus 네임스페이스의 메시징 단위를 늘리는 데 사용할 수 있는 샘플 PowerShell 스크립트는 다음과 같습니다. Automation Runbook의 이 PowerShell 스크립트는 MU를 1에서 2, 2에서 4 또는 4에서 8로 늘립니다. 이 속성의 허용된 값은 다음과 같습니다. 1, 2, 4 및 8. 다른 Runbook을 만들어 메시징 단위를 줄일 수 있습니다.

    **namespaceName** 및 **resourceGroupName** 매개 변수는 경고 시나리오와는 별도로 스크립트를 테스트하는 데 사용됩니다. 
    
    **WebHookData** 매개 변수는 런타임에서 리소스 그룹 이름, 리소스 이름 등의 정보를 전달하는 경고에 대한 것입니다. 

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData,
    
        [Parameter (Mandatory = $false)]
        [String] $namespaceName,
    
        [Parameter (Mandatory = $false)]
        [String] $resourceGroupName
    )
    
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the alert schema ID
        $schemaId = $WebhookBody.schemaId

        # If it's a metric alert
        if ($schemaId -eq "AzureMonitorMetricAlert") {

            # Get the resource group name from the alert context
            $resourceGroupName = $WebhookBody.resourceGroupName
            
            # Get the namespace name from the alert context
            $namespaceName = $WebhookBody.resourceName
        }
    }
    
    # Connect to Azure account
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    
    while(!($connectionResult) -And ($logonAttempt -le 10))
    {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =    Connect-AzAccount `
                                    -ServicePrincipal `
                                    -Tenant $connection.TenantID `
                                    -ApplicationId $connection.ApplicationID `
                                    -CertificateThumbprint $connection.CertificateThumbprint
    
        Start-Sleep -Seconds 30
    }
    
    # Get the current capacity (number of messaging units) of the namespace
    $sbusns=Get-AzServiceBusNamespace `
        -Name $namespaceName `
        -ResourceGroupName $resourceGroupName
    
    $currentCapacity = $sbusns.Sku.Capacity
    
    # Increase the capacity
    # Capacity can be one of these values: 1, 2, 4, 8
    if ($currentCapacity -eq 1) {
        $newMU = 2
    }
    elseif ($currentCapacity -eq 2) {
        $newMU = 4
    }
    elseif ($currentCapacity -eq 4) {
        $newMU = 8    
    }
    else {
    
    }
    
    # Update the capacity of the namespace
    Set-AzServiceBusNamespace `
            -Location eastus `
            -SkuName Premium `
            -Name $namespaceName `
            -SkuCapacity $newMU `
            -ResourceGroupName $resourceGroupName

    ```
2. **namespaceName** 및 **resourceGroupName** 매개 변수에 대한 값을 지정하여 [통합 문서를 테스트](../automation/manage-runbooks.md#test-a-runbook)합니다. 네임스페이스의 메시징 단위가 업데이트되었는지 확인합니다. 
3. 성공적으로 테스트한 후에는 나중에 네임스페이스에서 경고에 대한 작업으로 추가할 수 있도록 [통합 문서를 게시](..//automation/manage-runbooks.md#publish-a-runbook)합니다. 

## <a name="create-an-alert-on-the-namespace-to-trigger-the-runbook"></a>네임스페이스에 대한 경고를 만들어 Runbook 트리거
Service Bus 네임스페이스에 대한 경고를 구성하여 만든 Automation Runbook을 트리거하려면 [경고를 사용하여 Azure Automation Runbook 트리거](../automation/automation-create-alert-triggered-runbook.md) 문서를 참조하세요. 예를 들어 **네임스페이스 당 CPU 사용량** 또는 **네임스페이스 당 메모리 크기 사용량** 메트릭에 대한 경고를 생성하고, 만든 Automation Runbook을 트리거하는 작업을 추가할 수 있습니다. 이러한 메트릭에 대한 자세한 내용은 [리소스 사용량 메트릭](service-bus-metrics-azure-monitor.md#resource-usage-metrics)을 참조하세요.

다음 절차에서는 **네임스페이스** CPU 사용량이 **75%** 이상인 경우 Automation Runbook을 트리거하는 경고를 만드는 방법을 보여 줍니다.

1. 네임스페이스의 **Service Bus 네임스페이스** 페이지에서 왼쪽 메뉴에 있는 **경고**를 선택한 다음 도구 모음에서 **+새 경고 규칙**을 선택합니다. 
    
    ![경고 페이지 - 새 경고 규칙 단추](./media/automate-update-messaging-units/alerts-page.png)
2. **경고 규칙 만들기** 페이지에서 **조건 선택**을 클릭합니다. 

    ![경고 규칙 만들기 페이지 - 조건 선택](./media/automate-update-messaging-units/alert-rule-select-condition.png) 
3. **신호 논리 구성** 페이지에서 신호의 **CPU**를 선택합니다. 

    ![신호 논리 구성 - CPU 선택](./media/automate-update-messaging-units/configure-signal-logic.png)
4. **임계값**(이 예제에서는 **75%** )을 입력하고 **완료**를 선택합니다. 

    ![CPU 신호 구성](./media/automate-update-messaging-units/cpu-signal-configuration.png)
5. **경고 페이지 만들기**에서 **작업 그룹 선택**을 클릭합니다.
    
    ![작업 그룹 선택](./media/automate-update-messaging-units/select-action-group-button.png)
6. 도구 모음에서 **작업 그룹 만들기** 단추를 선택합니다. 

    ![작업 그룹 만들기 단추](./media/automate-update-messaging-units/create-action-group-button.png)
7. **작업 그룹 추가** 페이지에서 다음 단계를 수행합니다.
    1. 작업 그룹의 **이름**을 입력합니다. 
    2. 작업 그룹의 **짧은 이름**을 입력합니다.
    3. 작업 그룹을 만들려는 **구독**을 선택합니다.
    4. **리소스 그룹**을 선택합니다. 
    5. **작업** 섹션에서 **작업의 이름**을 입력하고 **작업 형식**에 대해 **Automation Runbook**을 선택합니다. 

        ![작업 그룹 페이지 추가](./media/automate-update-messaging-units/add-action-group-page.png)
8. **Runbook 구성** 페이지에서 다음 단계를 수행합니다.
    1. **Runbook 원본**에 대해 **사용자**를 선택합니다. 
    2. **구독**에 대해 Automation 계정을 포함하는 Azure **구독**을 선택합니다. 
    3. **Automation 계정**에 대해 **Automation 계정**을 선택합니다.
    4. **Runbook**에 대해 Runbook을 선택합니다. 
    5. **Runbook 구성** 페이지에서 **확인**을 선택합니다. 
        ![Runbook 구성](./media/automate-update-messaging-units/configure-runbook.png)
9. **작업 그룹 추가** 페이지에서 **확인**을 선택합니다. 
5. 이제 **경고 규칙 만들기** 페이지에서 **규칙의 이름**을 입력한 다음 **경고 규칙 만들기**를 선택합니다. 
    ![경고 규칙 만들기](./media/automate-update-messaging-units/create-alert-rule.png)

    > [!NOTE]
    > 이제 네임스페이스 CPU 사용량이 75를 초과하면 경고는 Automation Runbook을 트리거하여 Service Bus 네임스페이스의 메시징 단위를 늘립니다. 마찬가지로, 다른 Automation Runbook에 대한 경고를 만들 수 있으며, 네임스페이스 CPU 사용량이 25보다 적으면 메시징 단위가 줄어듭니다. 

## <a name="next-steps"></a>다음 단계
메시징 단위에 대한 자세한 내용은 [프리미엄 메시징](service-bus-premium-messaging.md)을 참조하세요.
