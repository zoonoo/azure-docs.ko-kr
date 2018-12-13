---
title: Azure Stack에 대 한 VPN gateway 설정 | Microsoft Docs
description: Azure Stack을 사용 하는 VPN 게이트웨이에 대 한 설정에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/14/2018
ms.author: sethm
ms.openlocfilehash: a770c88b294de24eb9e0f482681038e4d36b1d6f
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874603"
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>Azure Stack에 대 한 VPN gateway 구성 설정

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

VPN gateway는 Azure Stack에서 가상 네트워크와 원격 VPN gateway 간에 암호화 된 트래픽을 보내는 가상 네트워크 게이트웨이의 유형입니다. 원격 VPN gateway는 azure에서 데이터 센터에서 장치 또는 다른 사이트에서 장치를 수 있습니다.  두 끝점 간에 네트워크 연결이 없는 경우에 두 네트워크 간에 보안 사이트 간 (S2S) VPN 연결을 설정할 수 있습니다.

VPN Gateway 연결은 각각이 구성 가능한 설정을 포함하는 여러 리소스의 구성에 따라 좌우됩니다. 이 문서에서는 Resource Manager 배포 모델에서 만든 가상 네트워크에 대 한 VPN gateway와 관련 된 설정을 확인 하 고 리소스를 설명 합니다. 각 연결 솔루션에 대 한 설명 및 토폴로지 다이어그램을 찾을 수 있습니다 [Azure Stack에 대 한 VPN Gateway에 대 한](azure-stack-vpn-gateway-about-vpn-gateways.md)합니다.

## <a name="vpn-gateway-settings"></a>VPN gateway 설정

### <a name="gateway-types"></a>게이트웨이 유형

각 Azure Stack virtual network는 형식 이어야 하는 단일 가상 네트워크 게이트웨이 지원 **Vpn**합니다.  이 지원을 추가 형식을 지 원하는 Azure에서 서로 다릅니다.  

가상 네트워크 게이트웨이 만들 때 게이트웨이 유형이 구성에 대해 올바른지 확인 해야 합니다. VPN 게이트웨이 필요는 `-GatewayType Vpn`예를 들어:

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>게이트웨이 SKU

가상 네트워크 게이트웨이를 만들 때 사용하려는 게이트웨이 SKU를 지정해야 합니다. 작업 부하, 처리량, 기능 및 SLA의 종류를 기반으로 하는 요구 사항을 충족하는 SKU를 선택합니다.

Azure Stack은 VPN gateway Sku는 다음 표에 표시 된 것을 제공 합니다.

|   | VPN Gateway 처리량 |VPN Gateway 최대 IPsec 터널 |
|-------|-------|-------|
|**기본 SKU**  | 100Mbps  | 10    |
|**표준 SKU**           | 100Mbps  | 10    |
|**고성능 SKU** | 200Mbps    | 5 |

### <a name="resizing-gateway-skus"></a>게이트웨이 Sku 크기 조정

Azure Stack에는 sku는 지원 되는 레거시 Sku 간에 크기를 조정을 지원 하지 않습니다.

마찬가지로, Azure Stack (VpnGw1, VpnGw2 및 VpnGw3) Azure에서 지 원하는 최신 SKU로 조정에서 지원 되는 레거시 SKU (Basic, Standard 및 HighPerformance)를 지원 하지 않습니다.

### <a name="configure-the-gateway-sku"></a>게이트웨이 SKU 구성

#### <a name="azure-stack-portal"></a>Azure Stack 포털

Azure Stack 포털을 사용 하 여 Resource Manager 가상 네트워크 게이트웨이 만드는 경우에 드롭다운 목록을 사용 하 여 게이트웨이 SKU를 선택할 수 있습니다. 게이트웨이 형식 및 선택한 VPN 형식에 해당 하는 옵션 제공 됩니다.

#### <a name="powershell"></a>PowerShell

다음 PowerShell 예에서는 지정 된 **-GatewaySku** VpnGw1으로 합니다.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>연결 형식

Resource Manager 배포 모델에서 각 구성이 작동하려면 특정 가상 네트워크 게이트웨이 연결 유형이 필요합니다. 사용 가능한 Resource Manager PowerShell 값 **-ConnectionType** 됩니다.

* IPsec

다음 PowerShell 예제에서는 IPsec 연결 형식을 필요한 S2S 연결 생성 됩니다.

```PowerShell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>VPN 유형

VPN Gateway 구성에 대한 가상 네트워크 게이트웨이 만들 때 VPN 유형을 지정해야 합니다. 선택하는 VPN 유형은 만들려는 연결 토폴로지에 따라 달라집니다.  VPN 유형을 사용 하는 하드웨어에도 달라질 수 있습니다. S2S 구성에는 VPN 장치가 필요합니다. 일부 VPN 장치는 특정 VPN 유형을 지원합니다.

> [!IMPORTANT]  
> 현재 Azure Stack 경로 기반 VPN 유형을 지원합니다. 장치를 정책 기반 Vpn만 지 원하는 경우 Azure Stack에서 해당 장치에 대 한 연결 지원 되지 않습니다.  
>
> 또한 Azure Stack 지원 하지 않습니다 이때 경로 기반 게이트웨이 위한 정책 기반 트래픽 선택기를 사용 하 여 사용자 지정 IPSec/IKE 정책 구성에 지원 되지 않습니다.

* **PolicyBased**: 정책 기반 Vpn 암호화 하 고 온-프레미스 네트워크와 Azure Stack VNet 간의 주소 접두사의 조합으로 구성 된 IPsec 정책에 따라 IPsec 터널을 통해 패킷을 전달 합니다. 정책 또는 트래픽 선택기는 일반적으로 VPN 장치 구성에서 액세스 목록.

  >[!NOTE]
  >정책 기반 Azure Stack 아니라 Azure에서 지원 됩니다.

* **RouteBased**: IP 전달 또는 라우팅 테이블에 직접 패킷을 해당 터널 인터페이스에에서 구성 된 경로 기반 Vpn 사용 하 여 경로입니다. 그런 다음 터널 인터페이스는 터널로 들어오는 터널에서 나가는 패킷을 암호화하거나 암호 해독합니다. 정책 또는 트래픽 선택기 RouteBased Vpn에 대 한 any-임의의로 구성 된 (또는 와일드 카드를 사용 합니다.) 기본적으로 변경할 수 없습니다. 경로 기반 VPN 유형에 대 한 값에는 RouteBased입니다.

다음 PowerShell 예에서는 지정 된 **-vpntype은** 경로 기반으로 합니다. 게이트웨이 만들 때 해야 하는 **-vpntype은** 구성에 대 한 올바른 합니다.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>게이트웨이 요구 사항

다음 표에서 VPN gateway에 대 한 요구 사항을 나열합니다.

| |PolicyBased 기본 VPN Gateway | RouteBased 기본 VPN Gateway | RouteBased 표준 VPN Gateway | RouteBased 고성능 VPN Gateway|
|--|--|--|--|--|
| **사이트 간 연결 (S2S 연결)** | 지원되지 않음 | RouteBased VPN 구성 | RouteBased VPN 구성 | RouteBased VPN 구성 |
| **인증 방법**  | 지원되지 않음 | S2S 연결에 대 한 사전 공유 키  | S2S 연결에 대 한 사전 공유 키  | S2S 연결에 대 한 사전 공유 키  |   
| **S2S 연결의 최대 수**  | 지원되지 않음 | 10 | 10| 5|
|**활성 라우팅 지원(BGP)** | 지원되지 않음 | 지원되지 않음 | 지원됨 | 지원됨 |

### <a name="gateway-subnet"></a>게이트웨이 서브넷 

VPN Gateway를 만들기 전에 게이트웨이 서브넷을 만들어야 합니다. 게이트웨이 서브넷에 가상 네트워크 게이트웨이 Vm 및 서비스를 사용 하는 IP 주소입니다. 가상 네트워크 게이트웨이 만들 때 게이트웨이 VM은 게이트웨이 서브넷에 배포되고 필수 VPN Gateway 설정으로 구성됩니다. **없는** 게이트웨이 서브넷에 다른 작업 (예: 추가 Vm)를 배포 합니다.

>[!IMPORTANT]
>게이트웨이 서브넷이 제대로 작동하려면 이름을 **GatewaySubnet** 으로 지정해야 합니다. Azure Stack이이 이름을 사용 하 여 가상 네트워크 게이트웨이 Vm 및 서비스를 배포할 서브넷을 식별 합니다.

게이트웨이 서브넷을 만드는 경우 서브넷이 포함하는 IP 주소의 수를 지정합니다. 게이트웨이 서브넷의 IP 주소는 게이트웨이 VM 및 게이트웨이 서비스에 할당됩니다. 일부 구성에는 다른 구성보다 더 많은 IP 주소가 필요합니다. 만들려는 구성에 대한 지침을 검토하고 가지고 있는 만들려는 게이트웨이 서브넷이 해당 요구 사항을 충족하는지 확인합니다.

또한 게이트웨이 서브넷 추가 향후 구성을 처리할 충분 한 IP 주소가 있는지 확인 해야 합니다. 만들 수 있지만 게이트웨이 서브넷을 / 29만 작게 을/28 또는 더 큰 (/ 28, / 27, / 26, 및 등) 게이트웨이 서브넷을 만드는 것이 좋습니다. 이런 방식으로 나중에 기능을 추가할 경우 필요가 게이트웨이 삭제 하 고, 삭제 하 고, 더 많은 IP 주소를 허용 하도록 게이트웨이 서브넷을 다시 만듭니다.

다음 Resource Manager PowerShell 예제에서는 이름이 GatewaySubnet인 게이트웨이 서브넷을 보여 줍니다. CIDR 표기법이 /27을 지정하는 것을 확인할 수 있으며 이는 이번에 존재하는 대부분의 구성에 대한 충분한 IP 주소를 허용합니다.

```PowerShell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> 게이트웨이 서브넷에서 작업할 때는 게이트웨이 서브넷에 NSG(네트워크 보안 그룹)를 연결하지 않습니다. 이 서브넷에 네트워크 보안 그룹을 연결하면 VPN Gateway가 정상적으로 작동하지 않을 수 있습니다. 네트워크 보안 그룹에 대 한 자세한 내용은 참조 하세요. [네트워크 보안 그룹 이란?](/azure/virtual-network/virtual-networks-nsg)합니다.

### <a name="local-network-gateways"></a>로컬 네트워크 게이트웨이

Azure에서 VPN gateway 구성을 만들 때 로컬 네트워크 게이트웨이 종종 온-프레미스 위치를 나타냅니다. Azure Stack에서 Azure Stack 외부에 위치 하는 모든 원격 VPN 장치를 나타냅니다. VPN 장치에서 데이터 센터, 원격 데이터 센터 또는 Azure에서 VPN Gateway 수 있습니다.

로컬 네트워크 게이트웨이 VPN 장치의 공용 IP 주소에 이름을 지정 하 고 온-프레미스 위치에 있는 주소 접두사를 지정 합니다. Azure는 네트워크 트래픽에 대상 주소 접두사를 보고 로컬 네트워크 게이트웨이에 대해 지정한 구성을 참조하며 그에 따라 패킷을 라우팅합니다.

다음 PowerShell 예제에서는 새 로컬 네트워크 게이트웨이 만듭니다.

```PowerShell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

로컬 네트워크 게이트웨이 설정을 수정해야 하는 경우도 있습니다. 예를 들어 주소 범위를 추가 또는 수정할 경우 또는 VPN 장치의 IP 주소가 변경될 때가 여기에 해당합니다. [PowerShell을 사용하여 로컬 네트워크 게이트웨이 설정 수정](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway)을 참조하세요.

## <a name="ipsecike-parameters"></a>IPsec/IKE 매개 변수

Azure Stack에 VPN 연결을 설정 하면 양쪽 끝에서 연결을 구성 해야 합니다.  Azure Stack 및 스위치 또는 VPN 게이트웨이 역할을 하는 라우터 같은 하드웨어 장치 간에 VPN 연결을 구성 하는 경우 해당 장치 추가 설정의 요청할 수 있습니다.

개시자와 응답자 모두로 제품을 여러 개를 지 원하는, Azure와 달리 Azure Stack에 제품 하나에 지원 합니다.

### <a name="ike-phase-1-main-mode-parameters"></a>IKE 1단계(주 모드) 매개 변수

| 자산              | 값|
|-|-|
| IKE 버전           | IKEv2 |
|Diffie-Hellman 그룹   | 그룹 2(1024비트) |
| 인증 방법 | 미리 공유한 키 |
|암호화 및 해싱 알고리즘 | AES256, SHA256 |
|SA 수명(시간)     | 28,800초|

### <a name="ike-phase-2-quick-mode-parameters"></a>IKE 2단계(빠른 모드) 매개 변수

| 자산| 값|
|-|-|
|IKE 버전 |IKEv2 |
|암호화 및 해싱 알고리즘 (암호화)     | GCMAES256|
|암호화 및 해싱 알고리즘 (인증) | GCMAES256|
|SA 수명(시간)  | 27,000초  |
|SA 수명 (킬로바이트) | 33,553,408     |
|PFS(Perfect Forward Secrecy) |None<sup>참고 1 참조</sup> |
|작동하지 않는 피어 검색 | 지원됨|  

* *참고 1:* 버전 1807, Azure Stack 사용 하 여 PFS2048 값에 대 한는 Secrecy PFS (Perfect Forward).

## <a name="next-steps"></a>다음 단계

[ExpressRoute를 사용 하 여 연결](azure-stack-connect-expressroute.md)
