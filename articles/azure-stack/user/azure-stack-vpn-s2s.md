---
title: Azure Stack 사이트 간 VPN 연결 구성 | Microsoft Docs
description: Azure Stack에서 사이트 간 VPN 또는 VNet 대 VNet 연결에 대 한 IPsec/IKE 정책에 대해 알아보기
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: 9a14c819acbc46d8a281f73fd4f3185caa8a1e02
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478154"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-connections"></a>사이트 간 VPN 연결에 대 한 IPsec/IKE 정책 구성

사이트 및 사이트 간 (S2S) VPN에 대 한 IPsec/IKE 정책을 구성 하는 단계를 통해이 문서에서는 Azure Stack에 연결 합니다.

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>VPN gateway에 대 한 IPsec 및 IKE 정책 매개 변수

IPsec 및 IKE 프로토콜 표준은 다양 한 조합에 다양 한 암호화 알고리즘을 지원합니다. Azure Stack에서 지원 되는 매개 변수를 보려면 [IPsec/IKE 매개 변수](azure-stack-vpn-gateway-settings.md#ipsecike-parameters), 규정 준수 또는 보안 요구 사항을 충족 하는 데 도움이 됩니다.

이 문서에서는 IPsec/IKE 정책 구성 만들고 새로운 또는 기존 연결에 적용 하는 방법에 지침을 제공 합니다.

## <a name="considerations"></a>고려 사항

이러한 정책을 사용 하는 경우 다음 중요 사항을 note 합니다.

- IPsec/IKE 정책은에 작동 합니다 *표준* 하 고 *HighPerformance* (경로 기반) 게이트웨이 Sku입니다.

- 만 지정할 수 있습니다 **하나** 지정된 된 연결에 대 한 정책 조합 합니다.

- IKE(주 모드)와 IPsec(빠른 모드) 둘 다에 대한 모든 알고리즘 및 매개 변수를 지정해야 합니다. 부분 정책 지정은 허용되지 않습니다.

- 해당 VPN 디바이스 공급업체 사양을 참조하여 정책이 해당 온-프레미스 VPN 디바이스에서 지원되는지 확인하세요. 정책이 호환 되지 않는 경우에 사이트 간 연결을 설정할 수 없습니다.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a>1 부-IPsec/IKE 정책을 만들고 설정 하는 워크플로

이 섹션을 만들고 사이트 간 VPN 연결의 IPsec/IKE 정책을 업데이트 하는 데 필요한 워크플로 간략하게 설명 합니다.

1. 가상 네트워크 및 VPN gateway를 만듭니다.

2. 크로스-프레미스 연결에 대 한 로컬 네트워크 게이트웨이 만듭니다.

3. 선택한 알고리즘 및 매개 변수를 사용 하 여 IPsec/IKE 정책을 만듭니다.

4. IPsec/IKE 정책을 사용 하 여 IPSec 연결을 만듭니다.

5. 추가/업데이트/제거 기존 연결에 대 한 IPsec/IKE 정책입니다.

이 문서에서는 도움말의 지침에 설정 하 고 다음 그림에 표시 된 대로 IPsec/IKE 정책을 구성 합니다.

![설정 및 IPsec/IKE 정책 구성](media/azure-stack-vpn-s2s/site-to-site.png)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>2 부-지원 되는 암호화 알고리즘 및 키 수준

다음 표에서 지원 되는 암호화 알고리즘 및 키 강도가 구성할 수 있는 Azure Stack 고객:

| IPsec/IKEv2                                          | 옵션                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| IKEv2 암호화                                     | AES256, AES192, AES128, DES3, DES                                        |
| IKEv2 무결성                                      | SHA384, SHA256, SHA1, MD5                                                |
| DH 그룹                                             | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None         |
| IPsec 암호화                                     | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, 없음 |
| IPsec 무결성                                      | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                       |
| PFS 그룹                                            | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, 없음                         |
| QM SA 수명                                       | (선택 사항: 기본값은 사용을 지정 하지 않으면)<br />                         초 (정수, 최소 300/기본값 27,000 초)<br />                         사용량 (kb) (정수, 최소 1024/기본 102400000 사용량 (kb)) |                                                                          |
| 트래픽 선택기                                     | Azure Stack에서 정책 기반 트래픽 선택기를 사용 하는 것이 없습니다.         |

- 온-프레미스 VPN 디바이스 구성은 Azure IPsec/IKE 정책에서 지정한 다음 알고리즘 및 매개 변수가 일치하거나 포함해야 합니다.

  - IKE 암호화 알고리즘(기본 모드/1단계)
  - IKE 무결성 알고리즘(기본 모드/1단계)
  - DH 그룹(기본 모드/1단계)
  - IPsec 암호화 알고리즘(빠른 모드/2단계)
  - IPsec 무결성 알고리즘(빠른 모드/2단계)
  - PFS 그룹(빠른 모드/2단계)
  - SA 수명은 로컬 사양일 뿐이며 일치하지 않아도 됩니다.

- GCMAES가 IPsec 암호화 알고리즘의 경우와 사용 되, 동일한 GCMAES 알고리즘 및 키 길이 IPsec 무결성에 대해 선택 해야 합니다. 예를 들어, 둘 다에 대해 GCMAES128를 사용합니다.

- 앞의 표에서:

  - IKEv2는 기본 모드 또는 1단계에 해당합니다.
  - IPsec은 빠른 모드 또는 2단계에 해당합니다.
  - DH 그룹은 기본 모드 또는 1단계에서 사용되는 Diffie-Hellmen 그룹을 지정합니다.
  - PFS 그룹은 빠른 모드 또는 2단계에서 사용되는 Diffie-Hellmen 그룹을 지정했습니다.

- IKEv2 주 모드 SA 수명은 Azure Stack VPN gateway에서 28,800 초로 고정 됩니다.

다음 표에는 사용자 지정 정책에서 지원하는 해당 Diffie-hellman 그룹이 나열되어 있습니다.

| Diffie-Hellman 그룹 | DHGroup   | PFSGroup      | 키 길이    |
|----------------------|-----------|---------------|---------------|
| 1                    | DHGroup1  | PFS1          | 768비트 MODP  |
| 2                    | DHGroup2  | PFS2          | 1024비트 MODP |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | 2048비트 MODP |
| 19                   | ECP256    | ECP256        | 256비트 ECP   |
| 20                   | ECP384    | ECP384        | 384비트 ECP   |
| 24                   | DHGroup24 | PFS24         | 2048비트 MODP |

자세한 내용은 [RFC3526](https://tools.ietf.org/html/rfc3526) 하 고 [RFC5114](https://tools.ietf.org/html/rfc5114)합니다.

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>3 부-IPsec/IKE 정책을 사용 하 여 새 사이트 간 VPN 연결 만들기

이 섹션에서는 IPsec/IKE 정책을 사용 하 여 사이트 간 VPN 연결을 만드는 단계를 안내 합니다. 다음 단계를 다음 그림에 표시 된 대로 연결을 만듭니다.

![사이트-사이트 정책](media/azure-stack-vpn-s2s/site-to-site.png)

자세한 내용을 보려면 사이트 간 VPN 연결을 만들기 위한 단계별 지침을 참조 하세요 [사이트 간 VPN 연결을 만드는](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)합니다.

### <a name="prerequisites"></a>필수 조건

시작 하기 전에 다음 필수 구성 요소가 있는지 확인 합니다.

- Azure 구독. Azure 구독에 아직 없는 경우 활성화할 수 있습니다 하 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), 등록 또는 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)합니다.

- Azure Resource Manager PowerShell cmdlet입니다. 참조 [Azure Stack 용 PowerShell 설치](../azure-stack-powershell-install.md) PowerShell cmdlet 설치에 대 한 자세한 내용은 합니다.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>1단계 - 가상 네트워크, VPN Gateway 및 로컬 네트워크 게이트웨이 만들기

#### <a name="1-declare-variables"></a>1. 변수 선언

이 연습에서는 다음 변수를 선언 하 여 시작 합니다. 프로덕션에 대 한 구성 하는 경우 자리 표시자를 고유한 값으로 대체 해야 합니다.

```powershell
$Sub1 = "\<YourSubscriptionName\>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. 구독에 연결하고 새 리소스 그룹 만들기

리소스 관리자 cmdlet을 사용하려면 PowerShell 모드로 전환해야 합니다. 자세한 내용은 [사용자로 PowerShell 사용 하 여 Azure Stack에 연결](azure-stack-powershell-configure-user.md)합니다.

PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 샘플을 사용합니다.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. 가상 네트워크, VPN Gateway 및 로컬 네트워크 게이트웨이 만들기

다음 예제에서는 가상 네트워크를 만듭니다 **TestVNet1**세 개의 서브넷과 VPN gateway를 사용 하 여 합니다. 값으로 대체, 것이 중요 하면 항상 이름을 지정 하는 게이트웨이 서브넷 특별히 **GatewaySubnet**합니다. 다른 이름을 지정하는 경우 게이트웨이 만들기가 실패합니다.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>2 단계-IPsec/IKE 정책을 사용 하 여 사이트 간 VPN 연결 만들기

#### <a name="1-create-an-ipsecike-policy"></a>1. IPsec/IKE 정책 만들기

이 샘플 스크립트는 다음 알고리즘 및 매개 변수를 사용 하 여 IPsec/IKE 정책을 만듭니다.

- IKEv2: AES128, SHA1, DHGroup14
- IPsec: AES256, SHA256, 없음, SA 수명 14400 초 및 102400000KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

IPsec으로 GCMAES를 사용하는 경우 IPsec 암호화 및 무결성 모두에 대해 동일한 GCMAES 알고리즘 및 키 길이를 사용해야 합니다.

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2. IPsec/IKE 정책을 사용 하 여 사이트 간 VPN 연결 만들기

사이트 간 VPN 연결을 만들고 이전에 만든 IPsec/IKE 정책을 적용 합니다.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> 연결에 IPsec/IKE 정책이 지정되고 나면 Azure VPN Gateway는 해당 특정 연결에 지정된 암호화 알고리즘 및 키 수준으로 된 IPsec/IKE 제안만 보내거나 수락합니다. 온-프레미스 VPN 장치 연결에 대 한 사용 하거나 정확한 정책 조합을 사이트 간 VPN 터널을 설정할 수는이 고 그렇지를 허용 해야 합니다.

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>파트 4-연결에 대 한 업데이트 IPsec/IKE 정책

이전 섹션에는 기존 사이트 간 연결에 대 한 IPsec/IKE 정책을 관리 하는 방법을 보여 주었습니다. 다음 섹션에서는 연결에서 다음 작업을 안내 합니다.

1. 연결의 IPsec/IKE 정책 표시
2. 연결에 대한 IPsec/IKE 정책 추가 또는 업데이트
3. 연결에서 IPsec/IKE 정책 제거

> [!NOTE]
> IPsec/IKE 정책에서 지원 됩니다 *표준* 하 고 *HighPerformance* 경로 기반 VPN gateway만 합니다. 작동 하지 않습니다 합니다 *기본* 게이트웨이 SKU입니다.

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. 연결의 IPsec/IKE 정책 표시

다음 예제는 연결에 대해 IPsec/IKE 정책을 구성하는 방법을 보여 줍니다. 또한 스크립트는 이전 연습에서 계속:

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

마지막 명령은 있는 경우 연결에 구성 된 현재 IPsec/IKE 정책을 나열 합니다. 다음 예제는 연결에 대 한 샘플 출력:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

없는 경우 IPsec/IKE 정책이 없는 구성 명령을 `$connection6.policy` 빈 반환을 가져옵니다. 연결에서 IPsec/IKE 구성 되지 않았음을 의미 하지 않습니다. 사용자 지정 IPsec/IKE 정책이 없는 것을 의미 합니다. 실제 연결은 온-프레미스 VPN 디바이스 및 Azure VPN Gateway 간에 협상된 기본 정책을 사용합니다.

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. 연결에 대한 IPsec/IKE 정책 추가 또는 업데이트

새 정책 추가 또는 연결에서 기존 정책을 업데이트 하는 단계는 동일 합니다: 새 정책을 만든 다음 연결에 새 정책을 적용 합니다.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

정책이 업데이트 되었는지 확인 하려면 다시 연결을 가져올 수 있습니다.

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

마지막 줄의 출력은 다음 예제와 같습니다.

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. 연결에서 IPsec/IKE 정책 제거

Azure VPN gateway 되돌아갑니다 연결에서 사용자 지정 정책을 제거 하면, 합니다 [기본 IPsec/IKE 제안](azure-stack-vpn-gateway-settings.md#ipsecike-parameters), 온-프레미스 VPN 장치와 다시 협상 및 합니다.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = “AzS123”
$currentpolicy = $connection6.IpsecPolicies\[0\]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

같은 스크립트를 사용하여 연결에서 정책이 제거되었는지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack에 대 한 VPN gateway 구성 설정](azure-stack-vpn-gateway-settings.md)
