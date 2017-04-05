---
title: "ExpressRoute 회로에 라우팅을 구성하는 방법(피어링): Resource Manager: PowerShell: Azure | Microsoft Docs"
description: "이 문서에서는 Express 경로 회로의 개인, 공용 및 Microsoft 피어링을 만들고 프로비전하는 단계를 안내합니다. 또한 회로의 상태를 확인하고 업데이트 또는 삭제하는 방법을 보여줍니다."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0a036d51-77ae-4fee-9ddb-35f040fbdcdf
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 160560fcc3d586d2bbcba67d2f7c60cfed26f5c3
ms.lasthandoff: 03/24/2017


---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>PowerShell을 사용하여 ExpressRoute 회로의 피어링 만들기 및 수정
> [!div class="op_single_selector"]
> * [Resource Manager- Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-routing-arm.md)
> * [클래식- PowerShell](expressroute-howto-routing-classic.md)
> * [비디오 - 개인 피어링](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [비디오 - 공용 피어링](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [비디오 - Microsoft 피어링](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> 
> 

이 문서에서는 PowerShell 및 Azure Resource Manager 배포 모델을 사용하여 Express 경로 회로에 대한 라우팅 구성을 만들고 관리하는 단계를 안내합니다.  아래 단계에서는 ExpressRoute 회로에 대한 피어링의 상태를 확인, 업데이트 또는 삭제 및 프로비전 해제를 수행하는 방법도 설명합니다. 

**Azure 배포 모델 정보**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>필수 구성 요소
* 최신 버전의 Azure Resource Manager PowerShell cmdlet을 설치해야 합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)을 참조하세요. 
* 구성을 시작하기 전에 [필수 구성 요소](expressroute-prerequisites.md) 페이지, [라우팅 요구 사항](expressroute-routing.md) 페이지 및 [워크플로](expressroute-workflows.md) 페이지를 검토했는지 확인합니다.
* 활성화된 Express 경로 회로가 있어야 합니다. 지침을 수행하여 [Express 경로 회로를 만들고](expressroute-howto-circuit-arm.md) 진행하기 전에 연결 공급자를 통해 회로를 사용하도록 설정합니다. Express 경로 회로는 아래에 설명한 cmdlet을 실행할 수 있도록 프로비전되고 활성화된 상태여야 합니다.

이 지침은 2계층 연결 서비스를 제공하는 서비스 공급자를 사용하여 만든 회로에만 적용됩니다. 관리된 3계층 서비스(일반적으로 MPLS와 같은 IPVPN)를 제공하는 서비스 공급자를 사용하는 경우 연결 공급자는 사용자를 위해 라우팅을 구성하고 관리합니다.

> [!IMPORTANT]
> 현재는 서비스 관리 포털을 통해 서비스 공급자가 구성한 피어링을 보급하지 않습니다. 이 기능을 곧 사용할 수 있도록 개발 중입니다. BGP 피어링을 구성하기 전에 서비스 공급자에게 문의하십시오.
> 
> 

Express 경로 회로에 한 가지, 두 가지 또는 세 가지 피어링을 구성할 수 있습니다.(개인, Azure 공용 Azure 및 Microsoft) 선택한 순서로 피어링을 구성할 수 있습니다. 그러나 각 피어링의 구성을 한 번에 하나 씩 완료하도록 해야 합니다. 

## <a name="azure-private-peering"></a>Azure 개인 피어링
이 섹션에서는 Express 경로 회로에 Azure 개인 피어링 구성을 만들고 가져오며 업데이트 및 삭제하는 방법에 대한 지침을 제공합니다. 

### <a name="to-create-azure-private-peering"></a>Azure 개인 피어링을 만들려면
1. Express 경로에 대한 PowerShell 모듈을 가져옵니다.
   
     Express 경로 cmdlet을 사용하려면 [PowerShell 갤러리](http://www.powershellgallery.com/) 에서 최신 Powershell 설치 관리자를 설치하고 PowerShell 세션으로 Azure 리소스 관리자 모듈을 가져와야 합니다. 관리자 권한으로 PowerShell을 실행해야 합니다.
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    알려진 의미 체계 버전 범위의 모든 AzureRM.* 모듈을 가져옵니다.
   
        Import-AzureRM
   
    또한 알려진 의미 체계 버전 범위의 선택 모듈만 가져올 수 있습니다. 
   
        Import-Module AzureRM.Network 
   
    계정에 로그온합니다.
   
        Login-AzureRmAccount
   
    ExpressRoute 회로를 만들려는 구독을 선택합니다.
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. Express 경로 회로를 만듭니다.
   
    지침에 따라 [Express 경로 회로](expressroute-howto-circuit-arm.md) 를 만들고 연결 공급자를 통해 프로비전합니다. 
   
    연결 공급자가 관리된 3계층 서비스를 제공하는 경우 연결 공급자를 요청하여 Azure 개인 피어링을 사용하도록 할 수 있습니다. 이 경우에 다음 섹션에 나열된 지침에 따를 필요가 없습니다. 그러나 회로를 만든 후에 연결 공급자가 라우팅을 관리하지 않는 경우 아래 지침을 수행합니다. 
3. Express 경로 회로를 확인하여 프로비전되도록 합니다.
   
    먼저 Express 경로 회로가 프로비전되고 사용 가능한지 확인합니다. 아래 예제를 참조하세요.
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    응답은 아래 예제와 비슷합니다.
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []
4. 회로에 Azure 개인 피어링을 구성합니다.
   
    다음 단계를 계속 진행하기 전에 다음 항목이 있는지 확인합니다.
   
   * 기본 링크에 대한 /30 서브넷입니다. 가상 네트워크에 예약된 주소 공간의 일부가 아니어야 합니다.
   * 보조 링크에 대한 /30 서브넷입니다. 가상 네트워크에 예약된 주소 공간의 일부가 아니어야 합니다.
   * 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다. 이 피어링에 개인 AS 숫자를 사용할 수 있습니다. 65515를 사용하지 않는지 확인합니다.
   * 하나를 사용하기로 선택한 경우 MD5 해시를 사용합니다. **선택 사항입니다**.
     
    다음 cmdlet을 실행하여 회로에 Azure 개인 피어링을 구성할 수 있습니다.
     
          Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200
     
          Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
     
    MD5 해시를 사용하기로 선택한 경우 아래 cmdlet를 사용할 수 있습니다.
     
          Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

     
   > [!IMPORTANT]
   > 고객 ASN이 아닌 피어링 ASN로 AS 번호를 지정했는지 확인합니다.
   > 
   >


### <a name="to-view-azure-private-peering-details"></a>Azure 개인 피어링 세부 정보를 보려면
다음 cmdlet을 사용하여 구성 세부 정보를 가져올 수 있습니다.

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt    


### <a name="to-update-azure-private-peering-configuration"></a>Azure 개인 피어링 구성을 업데이트하려면
다음 cmdlet을 사용하여 구성의 일부를 업데이트할 수 있습니다. 아래 예제에서는 회로의 VLAN ID를 100개에서 500개로 업데이트 중입니다.

    Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-azure-private-peering"></a>Azure 개인 피어링을 삭제하려면
다음 cmdlet을 실행하여 피어링 구성을 제거할 수 있습니다.

> [!WARNING]
> 이 cmdlet을 실행하기 전에 모든 가상 네트워크가 Express 경로 회로에서 연결되지 않았는지 확인해야 합니다. 
> 
> 

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## <a name="azure-public-peering"></a>Azure 공용 피어링
이 섹션에서는 Express 경로 회로에 Azure 공용 피어링 구성을 만들고, 가져오며, 업데이트 및 삭제하는 방법에 대한 지침을 제공합니다.

### <a name="to-create-azure-public-peering"></a>Azure 공용 피어링을 만들려면
1. Express 경로에 대한 PowerShell 모듈을 가져옵니다.
   
     Express 경로 cmdlet을 사용하려면 [PowerShell 갤러리](http://www.powershellgallery.com/) 에서 최신 Powershell 설치 관리자를 설치하고 PowerShell 세션으로 Azure 리소스 관리자 모듈을 가져와야 합니다. 관리자 권한으로 PowerShell을 실행해야 합니다.
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    알려진 의미 체계 버전 범위의 모든 AzureRM.* 모듈을 가져옵니다.
   
        Import-AzureRM
   
    또한 알려진 의미 체계 버전 범위의 선택 모듈만 가져올 수 있습니다. 
   
        Import-Module AzureRM.Network 
   
    계정에 로그온합니다.
   
        Login-AzureRmAccount
   
    ExpressRoute 회로를 만들려는 구독을 선택합니다.
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. Express 경로 회로를 만듭니다.
   
    지침에 따라 [Express 경로 회로](expressroute-howto-circuit-arm.md) 를 만들고 연결 공급자를 통해 프로비전합니다. 
   
    연결 공급자가 관리된 3계층 서비스를 제공하는 경우 연결 공급자를 요청하여 Azure 공용 피어링을 사용하도록 할 수 있습니다. 이 경우에 다음 섹션에 나열된 지침에 따를 필요가 없습니다. 그러나 회로를 만든 후에 연결 공급자가 라우팅을 관리하지 않는 경우 아래 지침을 수행합니다.
3. Express 경로 회로를 확인하여 프로비전되도록 합니다.
   
    먼저 Express 경로 회로가 프로비전되고 사용 가능한지 확인합니다. 아래 예제를 참조하세요.
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    응답은 아래 예제와 비슷합니다.
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []    
4. 회로에 Azure 공용 피어링을 구성합니다.
   
       Make sure that you have the following information before you proceed further.
   
   * 기본 링크에 대한 /30 서브넷입니다. 유효한 공용 IPv4 접두사여야 합니다.
   * 보조 링크에 대한 /30 서브넷입니다. 유효한 공용 IPv4 접두사여야 합니다.
   * 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다.
   * 하나를 사용하기로 선택한 경우 MD5 해시를 사용합니다. **선택 사항입니다**.

    
    다음 cmdlet을 실행하여 회로에 Azure 공용 피어링을 구성할 수 있습니다.
     
          Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

          Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
     
    MD5 해시를 사용하기로 선택한 경우 아래 cmdlet를 사용할 수 있습니다.
     
          Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

          Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

     
> [!IMPORTANT]
> 고객 ASN이 아닌 피어링 ASN로 AS 번호를 지정했는지 확인합니다.
> 
>


### <a name="to-view-azure-public-peering-details"></a>Azure 공용 피어링 세부 정보를 보려면
다음 cmdlet을 사용하여 구성 세부 정보를 가져올 수 있습니다.

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt

### <a name="to-update-azure-public-peering-configuration"></a>Azure 공용 피어링 구성을 업데이트하려면
다음 cmdlet을 사용하여 구성의 일부를 업데이트할 수 있습니다.

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

아래 예제에서 회로의 VLAN ID를 200개에서 600개로 업데이트 중입니다.

### <a name="to-delete-azure-public-peering"></a>Azure 공용 피어링을 삭제하려면
다음 cmdlet을 실행하여 피어링 구성을 제거할 수 있습니다.

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="microsoft-peering"></a>Microsoft 피어링
이 섹션에서는 Express 경로 회로에 Microsoft 피어링 구성을 만들고, 가져오며, 업데이트 및 삭제하는 방법에 대한 지침을 제공합니다. 

### <a name="to-create-microsoft-peering"></a>Microsoft 피어링을 만들려면
1. Express 경로에 대한 PowerShell 모듈을 가져옵니다.
   
     Express 경로 cmdlet을 사용하려면 [PowerShell 갤러리](http://www.powershellgallery.com/) 에서 최신 Powershell 설치 관리자를 설치하고 PowerShell 세션으로 Azure 리소스 관리자 모듈을 가져와야 합니다. 관리자 권한으로 PowerShell을 실행해야 합니다.
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    알려진 의미 체계 버전 범위의 모든 AzureRM.* 모듈을 가져옵니다.
   
        Import-AzureRM
   
    또한 알려진 의미 체계 버전 범위의 선택 모듈만 가져올 수 있습니다. 
   
        Import-Module AzureRM.Network 
   
    계정에 로그온합니다.
   
        Login-AzureRmAccount
   
    ExpressRoute 회로를 만들려는 구독을 선택합니다.
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. Express 경로 회로를 만듭니다.
   
    지침에 따라 [Express 경로 회로](expressroute-howto-circuit-arm.md) 를 만들고 연결 공급자를 통해 프로비전합니다. 
   
    연결 공급자가 관리된 3계층 서비스를 제공하는 경우 연결 공급자를 요청하여 Azure 개인 피어링을 사용하도록 할 수 있습니다. 이 경우에 다음 섹션에 나열된 지침에 따를 필요가 없습니다. 그러나 회로를 만든 후에 연결 공급자가 라우팅을 관리하지 않는 경우 아래 지침을 수행합니다.
3. Express 경로 회로를 확인하여 프로비전되도록 합니다.
   
    먼저 Express 경로 회로가 프로비전되고 사용 가능한지 확인합니다. 아래 예제를 참조하세요.
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    응답은 아래 예제와 비슷합니다.
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []    
4. 회로에 Microsoft 피어링을 구성합니다.
   
    진행하기 전에 다음 정보가 있는지 확인합니다.
   
   * 기본 링크에 대한 /30 서브넷입니다. 사용자가 소유하고 RIR/IRR에 등록된 유효한 공용 IPv4 접두사여야 합니다.
   * 보조 링크에 대한 /30 서브넷입니다. 사용자가 소유하고 RIR/IRR에 등록된 유효한 공용 IPv4 접두사여야 합니다.
   * 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다.
   * 보급된 접두사: BGP 세션을 통해 보급하려는 모든 접두사 목록을 제공해야 합니다. 공용 IP 주소 접두사만 수락됩니다. 접두사 집합을 보내려는 경우에 쉼표로 구분 된 목록을 보낼 수 있습니다. 이 접두사는 RIR/IRR에 등록되어야 합니다.
   * 고객 ASN: 피어링 AS 숫자에 등록되지 않은 광고 접두사인 경우 등록된 AS 번호를 지정할 수 있습니다. **선택 사항입니다**.
   * 라우팅 레지스트리 이름: AS 번호 및 접두사가 등록된 RIR/ IRR를 지정할 수 있습니다.
   * 하나를 사용하기로 선택한 경우 MD5 해시를 사용합니다. **선택 사항입니다.**
     
      다음 cmdlet을 실행하여 회로에 Microsoft 피어링을 구성할 수 있습니다.
     
          Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"
     
          Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### <a name="to-get-microsoft-peering-details"></a>Microsoft 피어링 정보를 가져오려면
다음 cmdlet을 사용하여 구성 세부 정보를 가져올 수 있습니다.

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt


### <a name="to-update-microsoft-peering-configuration"></a>Microsoft 피어링 구성을 업데이트하려면
다음 cmdlet을 사용하여 구성의 일부를 업데이트할 수 있습니다.

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-microsoft-peering"></a>Microsoft 피어링을 삭제하려면
다음 cmdlet을 실행하여 피어링 구성을 제거할 수 있습니다.

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="next-steps"></a>다음 단계
다음 단계에서는 [VNet을 Express 경로 회로에 연결](expressroute-howto-linkvnet-arm.md)합니다.

* Express 경로 워크플로에 대한 자세한 내용은 [Express 경로 워크플로](expressroute-workflows.md)를 참조하세요.
* 회로 피어링에 대한 자세한 내용은 [Express 경로 회로 및 라우팅 도메인](expressroute-circuit-peerings.md)을 참조하세요.
* 가상 네트워크 작업에 대한 자세한 내용은 [가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요.


