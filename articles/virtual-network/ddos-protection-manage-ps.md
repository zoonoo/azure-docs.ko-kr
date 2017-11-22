---
title: "Azure PowerShell을 사용하여 Azure DDoS 보호 표준 관리 | Microsoft Docs"
description: "Azure PowerShell을 사용하여 Azure DDoS 보호 표준을 관리하는 방법을 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: baac97db61b84000557e7150a64ffb64d81ce00c
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2017
---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure DDoS 보호 표준 관리

DDoS(배포된 서비스 거부) 보호를 사용 및 사용하지 않도록 설정하는 방법과 원격 분석을 사용하여 Azure DDoS 보호 표준으로 DDoS 공격을 완화하는 방법에 대해 알아봅니다. DDoS 보호 표준은 할당된 Azure [공용 IP 주소](virtual-network-public-ip-address.md)가 있는 Application Gateway, 부하 분산 장치 및 가상 컴퓨터와 같은 Azure 리소스를 보호합니다. DDoS 보호 표준 및 해당 기능에 대한 자세한 내용은 [DDoS 보호 표준 개요](ddos-protection-overview.md)를 참조하세요. 

>[!IMPORTANT]
>Azure DDoS 보호 표준(DDoS 보호)은 현재 미리 보기로 제공됩니다. 제한된 수의 Azure 리소스는 DDoS 보호를 지원하며 선택한 수의 지역에서만 사용할 수 있습니다. 사용 가능한 지역 목록은 [DDoS 보호 표준 개요](ddos-protection-overview.md)를 참조하세요. 제한된 미리 보기 동안 구독에 대해 DDoS 보호를 사용하도록 설정하려면 [서비스에 등록](http://aka.ms/ddosprotection)해야 합니다. 등록 후 Azure DDoS 팀에서 사용 설정 프로세스를 안내하기 위해 연락합니다.


## <a name="log-in-to-azure"></a>Azure에 로그인

`Login-AzureRmAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다. Azure PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>DDoS 보호 표준 사용 - 새 가상 네트워크

DDoS 보호 기능이 사용하도록 설정된 가상 네트워크를 만들려면 다음 예제를 실행합니다.

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -EnableDDoSProtection
```

이 예제에서는 2개의 서브넷과 2개의 DNS 서버가 있는 가상 네트워크를 만듭니다. 가상 네트워크에서 DNS 서버를 지정하게 되면 이 가상 네트워크에 배포되는 NIC/VM이 기본적으로 이러한 DNS 서버를 상속합니다. 가상 네트워크에 있는 모든 보호된 리소스에 대해 DDoS 보호가 사용되도록 설정됩니다.

> [!WARNING]
> 지역 선택 시 [Azure DDoS 보호 표준 개요](ddos-protection-overview.md)에 있는 목록에서 지원되는 지역을 선택합니다.

## <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>기존 가상 네트워크에서 DDoS 보호 사용

기존 가상 네트워크에서 DDoS 보호를 사용하도록 설정하려면 다음 예제를 실행합니다.

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks -Force
```

> [!WARNING]
> 가상 네트워크는 지원되는 지역에 있어야 합니다. 지원되는 지역 목록은 [Azure DDoS 보호 표준 개요](ddos-protection-overview.md)를 참조하세요.

## <a name="disable-ddos-protection-on-a-virtual-network"></a>가상 네트워크에서 DDoS 보호 사용 안 함

가상 네트워크에서 DDoS 보호를 사용하지 않도록 설정하려면 다음 예제를 실행합니다.

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks" -Force
```

## <a name="review-the-ddos-protection-status-of-a-virtual-network"></a>가상 네트워크의 DDoS 보호 상태 검토 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>DDoS 보호 원격 분석 사용

공격에 대한 원격 분석이 Azure Monitor를 통해 실시간으로 제공됩니다. 이러한 원격 분석 기능은 공용 IP 주소의 위험이 완화되는 동안에만 사용할 수 있습니다. 공격이 완화되기 이전 또는 이후에는 원격 분석이 표시되지 않습니다.

### <a name="configure-alerts-on-ddos-protection-metrics"></a>DDoS 보호 메트릭에 대한 경고 구성

Azure Monitor 경고 구성을 통해 사용 가능한 DDoS 보호 메트릭을 선택하여 공격 중에 위험이 실제로 완화될 경우 알릴 수 있습니다.

#### <a name="configure-email-alert-rules-via-azure"></a>Azure를 통해 메일 경고 규칙 구성

1. 사용 가능한 구독 목록을 가져옵니다. 올바른 구독에서 작업 중인지 확인합니다. 그렇지 않은 경우 Get-AzureRmSubscription의 출력을 사용하여 올바른 항목을 선택합니다. 

    ```powershell
    Get-AzureRmSubscription 
    Get-AzureRmContext 
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

2. 리소스 그룹에 대한 기존 규칙을 나열하려면 다음 명령을 사용합니다. 

    ```powershell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

3. 규칙을 만들려면 먼저 다음 정보가 있어야 합니다. 

    - 경고를 설정할 리소스의 리소스 ID입니다.
    - 리소스에 대해 사용 가능한 메트릭 정의입니다. 리소스 ID를 가져오는 한 가지 방법은 Azure 포털을 사용하는 것입니다. 리소스를 이미 만들었다고 가정하고 Azure Portal에서 선택합니다. 그런 후 다음 페이지에서 *설정* 섹션의 *속성*을 선택합니다. **리소스 ID**는 다음 페이지의 필드입니다. 또 다른 방법은 [Azure Resource Explorer](https://resources.azure.com/)를 사용하는 것입니다. 공용 IP에 대한 예제 리소스 ID: `/subscriptions/<Id>/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    다음 예제에서는 가상 네트워크의 리소스에 연결된 공용 IP 주소에 대한 경고를 만듭니다. 경고를 만들 메트릭은 **DDoS 공격 상태이거나 그렇지 않은 상태**입니다. 부울 값 1 또는 0입니다. **1**은 공격을 받고 있는 것입니다. **0**은 공격을 받고 있지 않은 것입니다. 지난 5분 이내에 공격이 시작된 경우 경고가 생성됩니다.

    경고가 생성되었을 때 웹후크를 만들거나 메일을 보내려면 먼저 메일 및/또는 웹후크를 만듭니다. 메일 또는 웹후크를 만든 후 즉시 다음 예제와 같이 `-Actions` 태그를 사용하여 규칙을 만듭니다. 웹후크나 메일을 PowerShell을 사용하여 기존 규칙과 연결할 수 없습니다.

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/<Id>/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail -Description "Under DDoS Attack"
    ```

4. 규칙을 살펴서 경고가 제대로 만들어졌는지 확인합니다.

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    ```

경고를 만들기 위해 [웹후크 구성](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)에 대해 자세히 알아볼 수도 있습니다.

## <a name="configure-logging-on-ddos-protection-metrics"></a>DDoS 보호 메트릭에 대한 로깅 구성

PowerShell을 통해 Azure 진단 로깅을 액세스 및 구성하려면 [PowerShell 빠른 시작 예제](../monitoring-and-diagnostics/insights-powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 진단 로그에 대해 자세히 알아보기](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Storage에서 Log Analytics를 사용하여 로그 분석](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Event Hubs 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)