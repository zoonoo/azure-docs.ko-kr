---
title: "Azure VPN 게이트웨이에 연결하는 타사 VPN 장치 구성 정보 | Microsoft Docs"
description: "이 문서에서는 Azure VPN 게이트웨이에 연결하기 위한 타사 VPN 장치 구성에 대한 개요를 제공합니다."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 72dab85bb882b05d72cef26bef70437695b70416
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="overview-of-3rd-party-vpn-device-configurations"></a>타사 VPN 장치 구성의 개요
이 문서에서는 Azure VPN 게이트웨이에 연결하기 위한 온-프레미스 VPN 장치 구성에 대한 개요를 제공합니다. 예제 Azure Virtual Network 및 VPN 게이트웨이 설정을 사용하여 동일한 매개 변수로 다양한 온-프레미스 VPN 장치에 연결하겠습니다.

## <a name="device-requirements"></a>장치 요구 사항
Azure VPN 게이트웨이는 표준 IPsec/IKE 프로토콜 도구 모음을 사용하여 S2S VPN 터널을 설정합니다. 자세한 IPsec/IKE 프로토콜 매개 변수 및 Azure VPN 게이트웨이에 대한 기본 암호화 알고리즘은 [VPN 장치 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요. [암호화 요구 사항 정보](vpn-gateway-about-compliance-crypto.md)에 설명된 대로 특정 연결에 대한 암호화 알고리즘 및 키 길이의 정확한 조합을 선택적으로 지정할 수 있습니다.

## <a name ="singletunnel"></a>단일 VPN 터널
첫 번째 토폴로지는 Azure VPN 게이트웨이와 온-프레미스 VPN 장치 간에 단일 S2S VPN 터널로 구성됩니다. 필요에 따라 VPN 터널에서 BGP를 구성할 수 있습니다.

![단일 터널](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

자세한 단계별 지침은 [사이트 간 연결 구성](vpn-gateway-howto-site-to-site-resource-manager-portal.md)을 참조하세요. 다음 섹션에서는 매개 변수를 나열하고 시작하는 데 도움이 되도록 예제 PowerShell 스크립트를 제공합니다.

### <a name="network-and-vpn-gateway-information"></a>네트워크 및 VPN 게이트웨이 정보
이 섹션에서 위의 예제에 대한 매개 변수를 나열합니다.

| **매개 변수**                | **값**                    |
| ---                          | ---                          |
| VNet 주소 접두사        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN 게이트웨이 IP         | Azure VPN 게이트웨이 IP         |
| 온-프레미스 주소 접두사 | 10.51.0.0/16<br>10.52.0.0/16 |
| 온-프레미스 VPN 장치 IP    | 온-프레미스 VPN 장치 IP    |
| *VNet BGP ASN                | 65010                        |
| *Azure BGP 피어 IP           | 10.12.255.30                 |
| *온-프레미스 BGP ASN         | 65050                        |
| *온-프레미스 BGP 피어 IP     | 10.52.255.254                |

* (*) BGP에만 해당하는 선택적 매개 변수

### <a name="sample-powershell-script"></a>예제 PowerShell 스크립트
[PowerShell을 사용하여 S2S VPN 연결 만들기](vpn-gateway-create-site-to-site-rm-powershell.md)에 자세한 지침이 있습니다. 이 섹션에서는 시작하는 데 도움이 되도록 예제 스크립트를 제공합니다.

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subcription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a>[선택 사항] “UsePolicyBasedTrafficSelectors”로 사용자 지정 IPsec/IKE 정책 사용
VPN 장치에서 “임의” 트래픽 선택기(경로 기반/VTI 기반 구성)를 지원하지 않는 경우 [이 문서](vpn-gateway-connect-multiple-policybased-rm-ps.md)의 설명대로 사용자 지정 IPsec/IKE 정책을 만들고 “UsePolicyBasedTrafficSelectors” 옵션을 구성해야 합니다.

> [!IMPORTANT]
> 연결에서 "UsePolicyBasedTrafficSelectors" 옵션을 사용하기 위해 IPsec/IKE 정책을 만들어야 합니다.

아래 샘플 스크립트는 다음 알고리즘 및 매개 변수를 사용하여 IPsec/IKE 정책을 만듭니다.
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, SA 수명 7200초 및 20480000KB(20GB)

정책이 적용되고 연결에서 “UesPolicyBasedTrafficSelectors”가 사용되도록 설정됩니다.

```powershell
$ipsecpolicy5 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>[선택 사항] S2S VPN 연결에 BGP 사용
선택적으로, 연결에 BGP를 사용할 수 있습니다. 참조 [VPN 게이트웨이용 BGP](vpn-gateway-bgp-resource-manager-ps.md)를 참조하세요. 다음과 같은 두 가지 차이점이 있습니다.

온-프레미스 주소 접두사는 단일 호스트 주소, 즉 온-프레미스 BGP 피어 IP 주소일 수 있습니다.

```powershell
New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

연결을 만들 때 “-EnableBGP”를 $True로 설정해야 합니다.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

## <a name="next-steps"></a>다음 단계
활성-활성 크로스-프레미스 및 VNet 간 연결을 구성하는 단계는 [크로스-프레미스 및 VNet 간 연결에 대한 활성-활성 VPN Gateways 구성](vpn-gateway-activeactive-rm-powershell.md) 을 참조하세요.


