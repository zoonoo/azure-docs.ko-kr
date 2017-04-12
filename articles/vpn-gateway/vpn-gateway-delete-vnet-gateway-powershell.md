---
title: "가상 네트워크 게이트웨이 삭제: PowerShell: Azure Resource Manager | Microsoft Docs"
description: "Resource Manager 배포 모델에서 PowerShell을 사용하여 가상 네트워크 게이트웨이를 삭제합니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 3032b09d06a103f9cd915e3803355199243488f9
ms.lasthandoff: 03/21/2017


---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>PowerShell을 사용하여 가상 네트워크 삭제
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [클래식 - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

VPN 게이트웨이 구성에 대한 가상 네트워크 게이트웨이 삭제하려는 경우에 몇 가지 다른 접근 방법을 사용할 수 있습니다.

- 테스트 환경의 경우처럼 모든 항목을 삭제하고 다시 시작하려면 전체 리소스 그룹을 삭제할 수 있습니다. 리소스 그룹을 삭제하면 그룹 내의 모든 리소스가 삭제됩니다. 이 방법은 리소스 그룹에 리소스를 유지하지 않으려는 경우에만 권장됩니다. 이 방법을 사용할 때는 몇 가지 리소스만 선택적으로 삭제할 수가 없습니다.

- 리소스 그룹에 일부 리소스를 유지하려는 경우 가상 네트워크 게이트웨이를 삭제하는 작업이 좀 더 복잡해집니다. 가상 네트워크 게이트웨이를 삭제하려면 먼저 게이트웨이에 의존하는 모든 리소스를 삭제해야 합니다. 수행하는 단계는 만든 연결의 유형 및 각 연결에 대한 종속 리소스에 따라 달라집니다.

##<a name="before-beginning"></a>시작하기 전에

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. 최신 Azure Resource Manager PowerShell cmdlet을 다운로드합니다.
최신 버전의 Azure Resource Manager PowerShell cmdlet을 다운로드하고 설치합니다. PowerShell cmdlet 다운로드 및 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

### <a name="2-connect-to-your-azure-account"></a>2. Azure 계정에 연결합니다. 
PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

    Login-AzureRmAccount

계정에 대한 구독을 확인합니다.

    Get-AzureRmSubscription

둘 이상의 구독이 있는 경우 사용할 구독을 지정합니다.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

##<a name="S2S"></a>사이트 간 VPN 게이트웨이 삭제

S2S 구성에 대한 가상 네트워크 게이트웨이를 삭제하려면 먼저 가상 네트워크 게이트웨이와 관련된 각 리소스를 삭제해야 합니다. 리소스는 종속성으로 인해 특정 순서로 삭제해야 합니다. 아래 예제를 사용할 경우 일부 값은 구체적으로 호출해야 하지만 일부 값은 출력 결과에 해당합니다. 데모를 위해 예제에 다음과 같은 특정 값을 사용합니다.

VNet 이름: VNet1<br>
리소스 그룹 이름: RG1<br>
가상 네트워크 게이트웨이 이름: GW1<br>

다음 단계는 Resource Manager 배포 모델에 적용됩니다.

###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. 삭제하려는 가상 네트워크 게이트웨이를 가져옵니다.

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. 가상 네트워크 게이트웨이에 연결이 있는지 확인합니다.

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
    $Conns=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}

###<a name="3-delete-all-connections"></a>3. 모든 연결을 삭제합니다.
각 연결의 삭제를 확인하라는 메시지가 표시될 수 있습니다.

    $Conns | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}

###<a name="4-get-the-list-of-the-corresponding-local-network-gateways"></a>4. 해당 로컬 네트워크 게이트웨이 목록을 가져옵니다.

    $LNG=Get-AzureRmLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
    
###<a name="5-delete-the-local-network-gateways"></a>5. 로컬 네트워크 게이트웨이를 삭제합니다.
각 로컬 네트워크 게이트웨이의 삭제를 확인하라는 메시지가 표시될 수 있습니다.

    $LNG | ForEach-Object {Remove-AzureRmLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}

###<a name="6-delete-the-virtual-network-gateway"></a>6. 가상 네트워크 게이트웨이를 삭제합니다.
게이트웨이의 삭제를 확인하라는 메시지가 표시될 수 있습니다.

>[!NOTE]
> 이 VNet에 S2S 구성 외에 P2S 구성이 있는 경우 가상 네트워크 게이트웨이를 삭제하면 경고 없이 모든 P2S 클라이언트의 연결이 자동으로 끊어집니다.
>
>

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="7-get-the-ip-configurations-of-the-virtual-network-gateway"></a>7. 가상 네트워크 게이트웨이의 IP 구성을 가져옵니다.

    $GWIpConfigs = $Gateway.IpConfigurations

###<a name="8-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>8. 이 가상 네트워크 게이트웨이에 사용되는 공용 IP 주소 목록 가져오기 
가상 네트워크 게이트웨이가 활성-활성인 경우 두 개의 공용 IP 주소가 표시됩니다.

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="9-delete-the-public-ips"></a>9. 공용 IP를 삭제합니다.

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}

###<a name="10-delete-the-gateway-subnet-and-set-the-configuration"></a>10. 게이트웨이 서브넷을 삭제하고 구성을 설정합니다.

    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="v2v"></a>VNet 간 VPN 게이트웨이 삭제

V2V 구성에 대한 가상 네트워크 게이트웨이를 삭제하려면 먼저 가상 네트워크 게이트웨이와 관련된 각 리소스를 삭제해야 합니다. 리소스는 종속성으로 인해 특정 순서로 삭제해야 합니다. 아래 예제를 사용할 경우 일부 값은 구체적으로 호출해야 하지만 일부 값은 출력 결과에 해당합니다. 데모를 위해 예제에 다음과 같은 특정 값을 사용합니다.

VNet 이름: VNet1<br>
리소스 그룹 이름: RG1<br>
가상 네트워크 게이트웨이 이름: GW1<br>

다음 단계는 Resource Manager 배포 모델에 적용됩니다.

###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. 삭제하려는 가상 네트워크 게이트웨이를 가져옵니다.

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. 가상 네트워크 게이트웨이에 연결이 있는지 확인합니다.

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
 
다른 리소스 그룹의 일부인 가상 네트워크 게이트웨이에 대한 다른 연결이 있을 수 있습니다. 각 추가 리소스 그룹에서 추가 연결을 확인합니다. 이 예제에서 RG2의 연결을 확인합니다. 가상 네트워크 게이트웨이에 연결되어 있을 수 있는 각 리소스 그룹에 대해 이 작업을 실행합니다.

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}

###<a name="3-get-the-list-of-connections-in-both-directions"></a>3. 양방향으로 연결 목록을 가져옵니다.
VNet 간 구성이기 때문에 양방향 연결 목록이 필요합니다.

    $ConnsL=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
 
이 예제에서 RG2의 연결을 확인합니다. 가상 네트워크 게이트웨이에 연결되어 있을 수 있는 각 리소스 그룹에 대해 이 작업을 실행합니다.
 
    $ConnsR=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}

###<a name="4-delete-all-connections"></a>4. 모든 연결을 삭제합니다.
각 연결의 삭제를 확인하라는 메시지가 표시될 수 있습니다.

    $ConnsL | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
    $ConnsR | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}

###<a name="5-delete-the-virtual-network-gateway"></a>5. 가상 네트워크 게이트웨이를 삭제합니다.
가상 네트워크 게이트웨이의 삭제를 확인하라는 메시지가 표시될 수 있습니다.

>[!NOTE]
> VNet에 V2V 구성 외에 P2S 구성이 있는 경우 가상 네트워크 게이트웨이를 삭제하면 경고 없이 모든 P2S 클라이언트의 연결이 자동으로 끊어집니다.
>
>

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="6-get-the-ip-configurations-of-the-virtual-network-gateway"></a>6. 가상 네트워크 게이트웨이의 IP 구성을 가져옵니다.

    $GWIpConfigs = $Gateway.IpConfigurations

###<a name="7-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>7. 이 가상 네트워크 게이트웨이에 사용되는 공용 IP 주소 목록을 가져옵니다. 
가상 네트워크 게이트웨이가 활성-활성인 경우 두 개의 공용 IP 주소가 표시됩니다.

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="8-delete-the-public-ips"></a>8. 공용 IP를 삭제합니다.
공용 IP의 삭제를 확인하라는 메시지가 표시될 수 있습니다.

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}

###<a name="9-delete-the-gateway-subnet-and-set-the-configuration"></a>9. 게이트웨이 서브넷을 삭제하고 구성을 설정합니다.
 
    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="deletep2s"></a>지점 및 사이트 간 VPN Gateway 삭제

P2S 구성에 대한 가상 네트워크 게이트웨이를 삭제하려면 먼저 가상 네트워크 게이트웨이와 관련된 각 리소스를 삭제해야 합니다. 리소스는 종속성으로 인해 특정 순서로 삭제해야 합니다. 아래 예제를 사용할 경우 일부 값은 구체적으로 호출해야 하지만 일부 값은 출력 결과에 해당합니다. 데모를 위해 예제에 다음과 같은 특정 값을 사용합니다.

VNet 이름: VNet1<br>
리소스 그룹 이름: RG1<br>
가상 네트워크 게이트웨이 이름: GW1<br>

다음 단계는 Resource Manager 배포 모델에 적용됩니다.


>[!NOTE]
> VPN Gateway를 삭제하면 경고 없이 VNet에서 연결된 모든 클라이언트의 연결이 끊어집니다.
>
>

###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. 삭제하려는 가상 네트워크 게이트웨이를 가져옵니다.

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-delete-the-virtual-network-gateway"></a>2. 가상 네트워크 게이트웨이를 삭제합니다.
가상 네트워크 게이트웨이의 삭제를 확인하라는 메시지가 표시될 수 있습니다.

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="3-get-the-ip-configurations-of-the-virtual-network-gateway"></a>3. 가상 네트워크 게이트웨이의 IP 구성을 가져옵니다.

    $GWIpConfigs = $Gateway.IpConfigurations

###<a name="4-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>4. 이 가상 네트워크 게이트웨이에 사용되는 공용 IP 주소 목록을 가져옵니다. 
가상 네트워크 게이트웨이가 활성-활성인 경우 두 개의 공용 IP 주소가 표시됩니다.

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="5-delete-the-public-ips"></a>5. 공용 IP를 삭제합니다.
공용 IP의 삭제를 확인하라는 메시지가 표시될 수 있습니다.

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}

###<a name="6-delete-the-gateway-subnet-and-set-the-configuration"></a>6. 게이트웨이 서브넷을 삭제하고 구성을 설정합니다.
 
    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="delete"></a>리소스 그룹을 삭제하여 VPN 게이트웨이 삭제

리소스를 유지하지 않아도 되며 새로 시작하려는 경우 전체 리소스 그룹을 삭제할 수 있습니다. 모든 항목을 제거하는 빠른 방법입니다. 전체 리소스 그룹을 삭제하면 삭제하려는 리소스를 선택할 수 없습니다. 따라서 이 예제를 실행하기 전에 이 작업을 진정으로 원하는지 확인해야 합니다.

다음 단계는 Resource Manager 배포 모델에 적용됩니다.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. 구독 중인 모든 리소스 그룹 목록을 가져옵니다.

    Get-AzureRmResourceGroup
### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. 삭제할 리소스 그룹을 찾습니다.
삭제하려는 리소스 그룹을 찾고 해당 리소스 그룹의 리소스 목록을 확인합니다. 이 예제에서 리소스 그룹의 이름은 RG1입니다. 모든 리소스 목록을 검색하도록 예제를 수정합니다.

    Find-AzureRmResource -ResourceGroupNameContains RG1

### <a name="3-verify-the-resources-in-the-list"></a>3. 목록의 리소스를 확인합니다.
목록이 반환되면 리소스 그룹의 모든 리소스와 리소스 그룹 자체를 삭제할 것인지도 확인합니다. 

### <a name="4-delete-the-resource-group-and-resources"></a>4. 리소스 그룹 및 리소스를 삭제합니다.
리소스 그룹 및 리소스 그룹에 포함된 모든 리소스를 삭제하려면 예제를 수정하고 실행합니다.

    Remove-AzureRmResourceGroup -Name RG1

### <a name="5-check-the-status"></a>5. 상태를 확인합니다.
Azure에서 모든 리소스를 삭제하려면 다소 시간이 소요됩니다. 다음 cmdlet을 사용하여 리소스 그룹의 상태를 확인할 수 있습니다.

    Get-AzureRmResourceGroup -ResourceGroupName RG1

반환되는 결과에는 'Succeeded'가 표시됩니다.

    ResourceGroupName : RG1
    Location          : eastus
    ProvisioningState : Succeeded


