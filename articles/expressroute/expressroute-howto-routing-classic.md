---
title: '회로의 피어링 구성 - ExpressRoute: Azure: 클래식 | Microsoft Docs'
description: 이 문서에서는 ExpressRoute 회로의 프라이빗, 공용 및 Microsoft 피어링을 만들고 프로비전하는 단계를 안내합니다. 또한 회로의 상태를 확인하고 업데이트 또는 삭제하는 방법을 보여줍니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: d1662d17f37e668e989103989df9de49036bab6a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726204"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>ExpressRoute 회로의 피어링 만들기 및 수정(클래식)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [비디오 - 개인 피어링](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [비디오 - 공용 피어링](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [비디오 - Microsoft 피어링](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell(클래식)](expressroute-howto-routing-classic.md)
> 

이 문서에서는 PowerShell 및 클래식 배포 모델을 사용하여 ExpressRoute 회로에 대한 피어링/라우팅 구성을 만들고 관리하는 단계를 안내합니다. 아래 단계에서는 ExpressRoute 회로에 대한 피어링의 상태 확인, 업데이트 또는 삭제 및 프로비전 해제를 수행하는 방법도 설명합니다. ExpressRoute 회로에 한 가지, 두 가지 또는 세 가지 피어링을 구성할 수 있습니다(Azure 프라이빗, Azure 공용 및 Microsoft). 선택한 순서로 피어링을 구성할 수 있습니다. 그러나 각 피어링의 구성을 한 번에 하나 씩 완료하도록 해야 합니다. 

이러한 지침은 2계층 연결 서비스를 제공하는 서비스 공급자를 사용하여 만든 회로에만 적용됩니다. 관리된 3계층 서비스(일반적으로 MPLS와 같은 IPVPN)를 제공하는 서비스 공급자를 사용하는 경우 연결 공급자는 사용자를 위해 라우팅을 구성하고 관리합니다.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure 배포 모델 정보**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configuration-prerequisites"></a>필수 구성 요소

* 구성을 시작하기 전에 [필수 구성 요소](expressroute-prerequisites.md) 페이지, [라우팅 요구 사항](expressroute-routing.md) 페이지 및 [워크플로](expressroute-workflows.md) 페이지를 검토했는지 확인합니다.
* 활성화된 ExpressRoute 회로가 있어야 합니다. 지침을 수행하여 [ExpressRoute 회로를 만들고](expressroute-howto-circuit-classic.md) 진행하기 전에 연결 공급자를 통해 회로를 사용하도록 설정합니다. ExpressRoute 회로는 아래에 설명한 cmdlet을 실행할 수 있도록 프로비전되고 활성화된 상태여야 합니다.

### <a name="download-the-latest-powershell-cmdlets"></a>최신 PowerShell cmdlet 다운로드

최신 버전의 Azure SM(서비스 관리) PowerShell 모듈 및 ExpressRoute 모듈을 설치합니다. 다음 예제를 사용할 때는 최신 버전의 cmdlet이 출시되면 버전 번호(이 예제에서는 5.1.1)가 변경된다는 점에 주의하세요.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

더 자세한 내용은 [Azure PowerShell cmdlet 시작](/powershell/azure/overview)에서 Azure PowerShell 모듈을 사용하도록 컴퓨터를 구성하는 방법에 대한 단계별 지침을 참조하세요.

### <a name="sign-in"></a>로그인

Azure 계정에 로그인하려면 다음 예제를 사용합니다.

1. 상승된 권한으로 PowerShell 콘솔을 열고 계정에 연결합니다.

   ```powershell
   Connect-AzAccount
   ```
2. 계정에 대한 구독을 확인합니다.

   ```powershell
   Get-AzSubscription
   ```
3. 둘 이상의 구독이 있는 경우 사용할 구독을 선택합니다.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

4. 다음 cmdlet을 사용하여 클래식 배포 모델용 PowerShell에 Azure 구독을 추가합니다.

   ```powershell
   Add-AzureAccount
   ```

## <a name="azure-private-peering"></a>Azure 프라이빗 피어링

이 섹션에서는 ExpressRoute 회로에 Azure 프라이빗 피어링 구성을 만들고 가져오며 업데이트 및 삭제하는 방법에 대한 지침을 제공합니다. 

### <a name="to-create-azure-private-peering"></a>Azure 프라이빗 피어링을 만들려면

1. **ExpressRoute 회로를 만듭니다.**

   지침에 따라 [ExpressRoute 회로](expressroute-howto-circuit-classic.md) 를 만들고 연결 공급자를 통해 프로비전합니다. 연결 공급자가 관리된 3계층 서비스를 제공하는 경우 연결 공급자를 요청하여 Azure 프라이빗 피어링을 사용하도록 할 수 있습니다. 이 경우에 다음 섹션에 나열된 지침에 따를 필요가 없습니다. 그러나 회로를 만든 후에 연결 공급자가 라우팅을 관리하지 않는 경우 아래 지침을 수행합니다.
2. **ExpressRoute 회로를 확인하여 프로비전되도록 합니다.**
   
   ExpressRoute 회로가 프로비전되고 사용 가능한지 확인합니다.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   반환:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   회로가 프로비전 및 사용 가능으로 표시되는지 확인합니다. 그렇지 않은 경우 연결 공급자와 작업하여 회로를 필요한 상태로 가져옵니다.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **회로에 Azure 개인 피어링을 구성합니다.**

   다음 단계를 계속 진행하기 전에 다음 항목이 있는지 확인합니다.
   
   * 기본 링크에 대한 /30 서브넷입니다. 가상 네트워크에 예약된 주소 공간의 일부가 아니어야 합니다.
   * 보조 링크에 대한 /30 서브넷입니다. 가상 네트워크에 예약된 주소 공간의 일부가 아니어야 합니다.
   * 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다. 이 피어링에 프라이빗 AS 숫자를 사용할 수 있습니다. 65515를 사용하지 않는지 확인합니다.
   * 하나를 사용하기로 선택한 경우 MD5 해시를 사용합니다. **옵션**.
     
   다음 예제를 사용하여 회로에 Azure 프라이빗 피어링을 구성합니다.

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
   ```    

   MD5 해시를 사용하려는 경우 다음 예제를 사용하여 회로에 프라이빗 피어링을 구성합니다.

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
   ```
     
   > [!IMPORTANT]
   > 고객 ASN이 아닌 피어링 ASN으로 AS 번호를 지정했는지 확인합니다.
   > 

### <a name="to-view-azure-private-peering-details"></a>Azure 프라이빗 피어링 세부 정보를 보려면

다음 cmdlet을 사용하여 구성 세부 정보를 가져올 수 있습니다.

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

반환:

```
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 100
```

### <a name="to-update-azure-private-peering-configuration"></a>Azure 프라이빗 피어링 구성을 업데이트하려면

다음 cmdlet을 사용하여 구성의 일부를 업데이트할 수 있습니다. 다음 예제에서는 회로의 VLAN ID를 100개에서 500개로 업데이트하고 있습니다.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Azure 프라이빗 피어링을 삭제하려면

다음 cmdlet을 실행하여 피어링 구성을 제거할 수 있습니다. 이 cmdlet을 실행하기 전에 모든 가상 네트워크가 ExpressRoute 회로에서 연결되지 않았는지 확인해야 합니다.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Azure 공용 피어링

이 섹션에서는 ExpressRoute 회로에 Azure 공용 피어링 구성을 만들고, 가져오며, 업데이트 및 삭제하는 방법에 대한 지침을 제공합니다.

> [!NOTE]
> 새 회로 대 한 azure 공용 피어 링 하는 사용 되지 않습니다.
>

### <a name="to-create-azure-public-peering"></a>Azure 공용 피어링을 만들려면

1. **ExpressRoute 회로 만들기**

   지침에 따라 [ExpressRoute 회로](expressroute-howto-circuit-classic.md) 를 만들고 연결 공급자를 통해 프로비전합니다. 연결 공급자가 관리된 3계층 서비스를 제공하는 경우 연결 공급자를 요청하여 Azure 공용 피어링을 사용하도록 할 수 있습니다. 이 경우에 다음 섹션에 나열된 지침에 따를 필요가 없습니다. 그러나 회로를 만든 후에 연결 공급자가 라우팅을 관리하지 않는 경우 아래 지침을 수행합니다.
2. **ExpressRoute 회로를 확인하여 프로비전되는지 확인합니다.**

   먼저 ExpressRoute 회로가 프로비전되고 사용 가능한지 확인합니다.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   반환:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   회로가 프로비전 및 사용 가능으로 표시되는지 확인합니다. 그렇지 않은 경우 연결 공급자와 작업하여 회로를 필요한 상태로 가져옵니다.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
4. **회로에 Azure 공용 피어링 구성**
   
   진행하기 전에 다음 정보가 있는지 확인합니다.
   
   * 기본 링크에 대한 /30 서브넷입니다. 유효한 공용 IPv4 접두사여야 합니다.
   * 보조 링크에 대한 /30 서브넷입니다. 유효한 공용 IPv4 접두사여야 합니다.
   * 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다.
   * 하나를 사용하기로 선택한 경우 MD5 해시를 사용합니다. **옵션**.

   > [!IMPORTANT]
   > 고객 ASN이 아닌 피어링 ASN으로 AS 번호를 지정했는지 확인합니다.
   >  
     
   다음 예제를 사용하여 회로에 Azure 공용 피어링을 구성할 수 있습니다.

   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
   ```
     
   MD5 해시를 사용하려는 경우 다음 예제를 사용하여 회로를 구성합니다.
     
   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
   ```
     
### <a name="to-view-azure-public-peering-details"></a>Azure 공용 피어링 세부 정보를 보려면

구성 세부 정보를 보려면 다음 cmdlet을 사용합니다.

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

반환:

```powershell
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 131.107.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 131.107.0.4/30
State                          : Enabled
VlanId                         : 200
```

### <a name="to-update-azure-public-peering-configuration"></a>Azure 공용 피어링 구성을 업데이트하려면

다음 cmdlet을 사용하여 구성의 일부를 업데이트할 수 있습니다. 이 예제에서는 회로의 VLAN ID를 200개에서 600개로 업데이트됩니다.

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

회로가 프로비전 및 사용 가능으로 표시되는지 확인합니다. 
### <a name="to-delete-azure-public-peering"></a>Azure 공용 피어링을 삭제하려면

다음 cmdlet을 실행하여 피어링 구성을 제거할 수 있습니다.

```powershell
Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

## <a name="microsoft-peering"></a>Microsoft 피어링

이 섹션에서는 ExpressRoute 회로에 Microsoft 피어링 구성을 만들고, 가져오며, 업데이트 및 삭제하는 방법에 대한 지침을 제공합니다. 

### <a name="to-create-microsoft-peering"></a>Microsoft 피어링을 만들려면

1. **ExpressRoute 회로 만들기**
  
   지침에 따라 [ExpressRoute 회로](expressroute-howto-circuit-classic.md) 를 만들고 연결 공급자를 통해 프로비전합니다. 연결 공급자가 관리된 3계층 서비스를 제공하는 경우 연결 공급자를 요청하여 Azure 프라이빗 피어링을 사용하도록 할 수 있습니다. 이 경우에 다음 섹션에 나열된 지침에 따를 필요가 없습니다. 그러나 회로를 만든 후에 연결 공급자가 라우팅을 관리하지 않는 경우 아래 지침을 수행합니다.
2. **ExpressRoute 회로를 확인하여 프로비전되는지 확인합니다.**

   회로가 프로비전 및 사용 가능으로 표시되는지 확인합니다. 
   
   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   반환:
   
   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   회로가 프로비전 및 사용 가능으로 표시되는지 확인합니다. 그렇지 않은 경우 연결 공급자와 작업하여 회로를 필요한 상태로 가져옵니다.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **회로에 Microsoft 피어링 구성**
   
    진행하기 전에 다음 정보가 있는지 확인합니다.
   
   * 기본 링크에 대한 /30 서브넷입니다. 사용자가 소유하고 RIR/IRR에 등록된 유효한 공용 IPv4 접두사여야 합니다.
   * 보조 링크에 대한 /30 서브넷입니다. 사용자가 소유하고 RIR/IRR에 등록된 유효한 공용 IPv4 접두사여야 합니다.
   * 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다.
   * 보급된 접두사: BGP 세션을 통해 보급하려는 모든 접두사 목록을 제공해야 합니다. 공용 IP 주소 접두사만 수락됩니다. 접두사 집합을 보내려는 경우 쉼표로 구분된 목록을 보낼 수 있습니다. 이 접두사는 RIR/IRR에 등록되어야 합니다.
   * 고객 ASN: 피어링 AS 숫자에 등록되지 않은 광고 접두사인 경우 등록된 AS 번호를 지정할 수 있습니다. **옵션**.
   * 라우팅 레지스트리 이름: AS 번호 및 접두사가 등록된 RIR/ IRR를 지정할 수 있습니다.
   * 하나를 사용하기로 선택한 경우 MD5 해시를 사용합니다. **선택**
     
   다음 cmdlet을 실행하여 회로에 Microsoft 피어링을 구성합니다.
 
   ```powershell
   New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
   ```

### <a name="to-view-microsoft-peering-details"></a>Microsoft 피어링 세부 정보를 보려면

다음 cmdlet을 사용하여 구성 세부 정보를 가져올 수 있습니다.

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
반환:

```powershell
AdvertisedPublicPrefixes       : 123.0.0.0/30
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 2245
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : ARIN
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 300
```

### <a name="to-update-microsoft-peering-configuration"></a>Microsoft 피어링 구성을 업데이트하려면

다음 cmdlet을 사용하여 구성의 일부를 업데이트할 수 있습니다.

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>Microsoft 피어링을 삭제하려면

다음 cmdlet을 실행하여 피어링 구성을 제거할 수 있습니다.

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>다음 단계

이제 [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-classic.md)합니다.

* 워크플로에 대한 자세한 내용은 [ExpressRoute 워크플로](expressroute-workflows.md)를 참조하세요.
* 회로 피어링에 대한 자세한 내용은 [ExpressRoute 회로 및 라우팅 도메인](expressroute-circuit-peerings.md)을 참조하세요.
