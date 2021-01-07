---
title: 'S2S VPN에 대 한 IPsec/IKE 정책 & VNet 간 연결: Azure Portal'
titleSuffix: Azure VPN Gateway
description: Azure Resource Manager 및 Azure Portal를 사용 하 여 Azure VPN gateway를 통해 S2S 또는 VNet 간 연결에 대 한 IPsec/IKE 정책을 구성 합니다.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: eda920640667abc6620c5c90ee7d04a44789353e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90996759"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections-azure-portal"></a>S2S VPN 또는 VNet 간 연결에 대 한 IPsec/IKE 정책 구성: Azure Portal

이 문서에서는 Azure Portal를 사용 하 여 사이트 간 VPN 또는 VNet 간 연결 VPN Gateway에 대 한 IPsec/IKE 정책을 구성 하는 단계를 안내 합니다. 다음 섹션에서는 IPsec/IKE 정책을 만들고 구성 하 고 새 연결 또는 기존 연결에 정책을 적용 하는 방법을 설명 합니다.

## <a name="about-ipsec-and-ike-policy-parameters"></a><a name="about"></a>IPsec 및 IKE 정책 매개 변수 정보

IPsec 및 IKE 프로토콜 표준은 다양하게 결합된 다양한 암호화 알고리즘을 지원합니다. 준수 또는 보안 요구 사항을 충족 하기 위해 크로스-프레미스 및 VNet 간 연결을 보장 하는 데 도움이 되는 방법에 [대 한 자세한 내용은 암호화 요구 사항 및 AZURE VPN 게이트웨이 정보](vpn-gateway-about-compliance-crypto.md) 를 참조 하세요.

이 문서에서는 IPsec/IKE 정책을 만들고 구성 하는 지침을 제공 하 고 새로운 또는 기존 VPN Gateway 연결에 적용 합니다.

### <a name="considerations"></a>고려 사항

* IPsec/IKE 정책은 다음 게이트웨이 Sku에만 작동 합니다.
  * ***VpnGw1 ~ 5 및 VpnGw1AZ ~ 5AZ***
  * ***Standard*** 및 ***highperformance***
* 지정된 연결에 대해 ***하나의*** 정책 조합만 지정할 수 있습니다.
* IKE(주 모드)와 IPsec(빠른 모드) 둘 다에 대한 모든 알고리즘 및 매개 변수를 지정해야 합니다. 부분 정책 지정은 허용되지 않습니다.
* 해당 VPN 디바이스 공급업체 사양을 참조하여 정책이 해당 온-프레미스 VPN 디바이스에서 지원되는지 확인하세요. 정책이 호환되지 않는 경우 S2S 또는 VNet 간 연결을 설정할 수 없습니다.

## <a name="workflow"></a><a name ="workflow"></a>워크플로

이 섹션에서는 S2S VPN 또는 VNet 간 연결에 대한 IPsec/IKE 정책을 만들고 업데이트하는 워크플로를 설명합니다.

1. 가상 네트워크 및 VPN gateway를 만듭니다.
2. 크로스-프레미스 연결에 대 한 로컬 네트워크 게이트웨이 또는 VNet 간 연결에 대 한 다른 가상 네트워크 및 게이트웨이를 만듭니다.
3. 연결 (IPsec 또는 VNet2VNet)을 만듭니다.
4. 연결 리소스에서 IPsec/IKE 정책을 구성/업데이트/제거 합니다.

이 문서의 지침은 다이어그램에 표시 된 대로 IPsec/IKE 정책을 설정 하 고 구성 하는 데 도움이 됩니다.

:::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE 정책 다이어그램" border="false":::

## <a name="supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>키 강도 & 지원 되는 암호화 알고리즘

### <a name="algorithms-and-keys"></a><a name ="table1"></a>알고리즘 및 키

다음 표에는 고객이 구성 가능하도록 지원되는 암호화 알고리즘 및 키 강도가 나와 있습니다.

| **IPsec/IKE**    | **옵션**    |
| ---              | ---            |
| IKE 암호화   | AES256, AES192, AES128, DES3, DES                  |
| IKE 무결성    | SHA384, SHA256, SHA1, MD5                          |
| DH 그룹         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, 없음 |
| IPsec 암호화 | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, 없음    |
| IPsec 무결성  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS 그룹        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, 없음   |
| QM SA 수명   | (**선택 사항**: 지정되지 않으면 기본값이 사용됨)<br>초(정수, **최소 300**/기본값 27,000초)<br>KB(정수, **최소 1,024**/기본값 102,400,000KB)    |
| 트래픽 선택기 | UsePolicyBasedTrafficSelectors** ($True/$False - **선택 사항**, 지정되지 않으면 기본값 $False)    |
| LPD 시간 제한      | 초 (정수: 최소 9/최대 3600; 기본값 45 초) |
|  |  |

#### <a name="important-requirements"></a>중요 요구 사항

* 온-프레미스 VPN 디바이스 구성은 Azure IPsec/IKE 정책에서 지정한 다음 알고리즘 및 매개 변수가 일치하거나 포함해야 합니다.
  * IKE 암호화 알고리즘(기본 모드/1단계)
  * IKE 무결성 알고리즘(기본 모드/1단계)
  * DH 그룹(기본 모드/1단계)
  * IPsec 암호화 알고리즘(빠른 모드/2단계)
  * IPsec 무결성 알고리즘(빠른 모드/2단계)
  * PFS 그룹 (빠른 모드/2 단계) > * 트래픽 선택기 (UsePolicyBasedTrafficSelectors가 사용 되는 경우)
  * SA 수명은 로컬 사양일 뿐이며 일치하지 않아도 됩니다.

* GCMAES가 IPsec 암호화 알고리즘에 사용 되는 경우 동일한 GCMAES 알고리즘과 IPsec 무결성에 대 한 키 길이를 선택 해야 합니다. 예를 들어 두 가지 모두에 대해 GCMAES128를 사용 합니다.

* 위의 [알고리즘과 키 테이블](#table1) 에서 다음을 수행 합니다.
  * IKE는 주 모드 또는 1 단계에 해당 합니다.
  * IPsec은 빠른 모드 또는 2단계에 해당합니다.
  * DH 그룹은 기본 모드 또는 1단계에서 사용되는 Diffie-Hellmen 그룹을 지정합니다.
  * PFS 그룹은 빠른 모드 또는 2단계에서 사용되는 Diffie-Hellmen 그룹을 지정했습니다.

* IKE 주 모드 SA 수명은 Azure VPN 게이트웨이에서 28800 초에 고정 됩니다.

* **UsePolicyBasedTrafficSelectors** 를 연결에 $True 설정 하면 온-프레미스의 정책 기반 VPN 방화벽에 연결 하도록 Azure VPN gateway가 구성 됩니다. PolicyBasedTrafficSelectors를 사용하도록 설정한 경우 VPN 디바이스에 온-프레미스 네트워크(로컬 네트워크 게이트웨이) 접두사 및 Azure Virtual Network 접두사 간의 모든 조합으로 정의된 일치하는 트래픽 선택기가 있는지 확인해야 합니다. 예를 들어 온-프레미스 네트워크 접두사가 10.1.0.0/16 및 10.2.0.0/16이고 가상 네트워크 접두사가 192.168.0.0/16 및 172.16.0.0/16이면 다음 트래픽 선택기를 지정해야 합니다.
  * 10.1.0.0/16 <====> 192.168.0.0/16
  * 10.1.0.0/16 <====> 172.16.0.0/16
  * 10.2.0.0/16 <====> 192.168.0.0/16
  * 10.2.0.0/16 <====> 172.16.0.0/16

   정책 기반 트래픽 선택기에 대한 자세한 내용은 [여러 온-프레미스 정책 기반 VPN 디바이스 연결](vpn-gateway-connect-multiple-policybased-rm-ps.md)을 참조하세요.

* LPD timeout-Azure VPN 게이트웨이의 기본값은 45 초입니다. 제한 시간을 더 짧게 설정 하면 IKE에서 더 적극적으로 다시 대조 하 여 연결이 끊어진 상태로 표시 됩니다. 이는 VPN gateway가 있는 Azure 지역에서 온-프레미스 위치가 멀리 떨어져 있거나 실제 링크 조건으로 인해 패킷 손실이 발생 하는 경우에는 적합 하지 않을 수 있습니다. 일반적인 권장 사항은 **30 ~ 45** 초 사이의 제한 시간을 설정 하는 것입니다.

### <a name="diffie-hellman-groups"></a>Diffie-Hellman 그룹

다음 표에는 사용자 지정 정책에서 지원하는 해당 Diffie-hellman 그룹이 나열되어 있습니다.

| **Diffie-Hellman 그룹**  | **DHGroup**              | **PFSGroup** | **키 길이** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768비트 MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024비트 MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048비트 MODP  |
| 19                        | ECP256                   | ECP256       | 256비트 ECP    |
| 20                        | ECP384                   | ECP384       | 384비트 ECP    |
| 24                        | DHGroup24                | PFS24        | 2048비트 MODP  |

자세한 내용은 [RFC3526](https://tools.ietf.org/html/rfc3526) 및 [RFC5114](https://tools.ietf.org/html/rfc5114)를 참조하세요.

## <a name="s2s-vpn-with-ipsecike-policy"></a><a name ="S2S"></a>IPsec/IKE 정책을 사용 하는 S2S VPN

이 섹션에서는 IPsec/IKE 정책을 사용 하 여 사이트 간 VPN 연결을 만드는 단계를 안내 합니다. 다음 단계는 다음 다이어그램에 표시 된 대로 연결을 만듭니다.

:::image type="content" source="./media/ipsec-ike-policy-howto/site-to-site-diagram.png" alt-text="IPsec/IKE 정책 다이어그램" border="false":::

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>1단계 - 가상 네트워크, VPN Gateway 및 로컬 네트워크 게이트웨이 만들기

아래 스크린샷에 표시 된 것 처럼 다음 리소스를 만듭니다. 단계는 사이트 간 [VPN 연결 만들기](vpn-gateway-howto-site-to-site-resource-manager-portal.md)를 참조 하세요.

* **가상 네트워크:**  TestVNet1

   :::image type="content" source="./media/ipsec-ike-policy-howto/testvnet-1.png" alt-text="IPsec/IKE 정책 다이어그램":::

* **VPN gateway:** VNet1GW

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-1-gateway.png" alt-text="IPsec/IKE 정책 다이어그램":::

* **로컬 네트워크 게이트웨이:** Site6

   :::image type="content" source="./media/ipsec-ike-policy-howto/lng-site-6.png" alt-text="IPsec/IKE 정책 다이어그램":::

* **연결:** VNet1 Site6

    :::image type="content" source="./media/ipsec-ike-policy-howto/connection-site-6.png" alt-text="IPsec/IKE 정책 다이어그램":::

### <a name="step-2---configure-ipsecike-policy-on-the-s2s-vpn-connection"></a><a name="s2sconnection"></a>2 단계-S2S VPN 연결에서 IPsec/IKE 정책 구성

이 섹션에서는 다음 알고리즘 및 매개 변수를 사용 하 여 IPsec/IKE 정책을 구성 합니다.

* IKE: AES256, SHA384, DHGroup24, LPD timeout 45 초
* IPsec: AES256, SHA256, PFS 없음, SA 수명 3만 초 및 102400000KB

1. Azure Portal 연결 리소스 ( **VNet1toSite6**)로 이동 합니다. **구성** 페이지를 선택 하 고 **사용자 지정** IPsec/IKE 정책을 선택 하 여 모든 구성 옵션을 표시 합니다. 아래 스크린샷은 목록에 따라 구성을 보여 줍니다.

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-site-6.png" alt-text="IPsec/IKE 정책 다이어그램":::

1. IPsec으로 GCMAES를 사용하는 경우 IPsec 암호화 및 무결성 모두에 대해 동일한 GCMAES 알고리즘 및 키 길이를 사용해야 합니다. 예를 들어 아래 스크린샷은 IPsec 암호화와 IPsec 무결성 모두에 대해 GCMAES128를 지정 합니다.

   :::image type="content" source="./media/ipsec-ike-policy-howto/gcmaes.png" alt-text="IPsec/IKE 정책 다이어그램":::

1. 위에서 설명한 대로 **정책 기반 트래픽 선택기 사용** 옵션으로 **사용** 을 선택 하 여 Azure VPN gateway를 온-프레미스의 정책 기반 VPN 장치에 연결할 수 있습니다.

   :::image type="content" source="./media/ipsec-ike-policy-howto/policy-based-selector.png" alt-text="IPsec/IKE 정책 다이어그램":::

1. 모든 옵션을 선택한 후에는 **저장** 을 선택 하 여 연결 리소스의 변경 내용을 커밋합니다. 정책은 약 1 분 후에 적용 됩니다.

> [!IMPORTANT]
>
> * 연결에 IPsec/IKE 정책이 지정되고 나면 Azure VPN Gateway는 해당 특정 연결에 지정된 암호화 알고리즘 및 키 수준으로 된 IPsec/IKE 제안만 보내거나 수락합니다. 연결에 대한 온-프레미스 VPN 디바이스에서 정확한 정책 조합을 사용하거나 수락하는지 확인합니다. 그러지 않으면 S2S VPN 터널이 설정되지 않습니다.
>
> * 위의 스크린샷에 표시 된 대로 사용자 지정 IPsec/IKE 정책을 사용 하지 않고 **정책 기반 트래픽 선택기** 및 **lpd Timeout** 옵션을 **기본** 정책으로 지정할 수 있습니다.
>

## <a name="vnet-to-vnet-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>IPsec/IKE 정책을 사용 하 여 vnet 간

IPsec/IKE 정책을 사용 하 여 VNet 간 연결을 만드는 단계는 S2S VPN 연결의 단계와 유사 합니다.

:::image type="content" source="./media/ipsec-ike-policy-howto/vnet-policy.png" alt-text="IPsec/IKE 정책 다이어그램" border="false":::

1. Vnet 간 [연결 만들기](vpn-gateway-vnet-vnet-rm-ps.md) 문서의 단계를 사용 하 여 vnet 간 연결을 만듭니다.

2. 단계를 완료 한 후 VNet2GW 리소스에서 아래 스크린샷에 표시 된 것 처럼 두 개의 VNet 간 연결이 표시 됩니다.

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-connections.png" alt-text="IPsec/IKE 정책 다이어그램":::

3. 연결 리소스로 이동 하 고 포털의 **구성** 페이지로 이동 합니다. **IPsec/IKE 정책** 에서 **사용자 지정** 을 선택 하 여 사용자 지정 정책 옵션을 표시 합니다. 해당 키 길이가 인 암호화 알고리즘을 선택 합니다.

   스크린 샷에서는 다음과 같은 알고리즘 및 매개 변수를 사용 하는 다른 IPsec/IKE 정책을 보여 줍니다.
   * IKE: AES128, SHA1, DHGroup14, 6PD timeout 45 초
   * IPsec: GCMAES128, GCMAES128, PFS14, SA 수명 14400초 및 102400000KB

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-policy.png" alt-text="IPsec/IKE 정책 다이어그램":::

4. **저장** 을 선택 하 여 연결 리소스에 정책 변경 내용을 적용 합니다.

5. 다른 연결 리소스 VNet2toVNet1에 동일한 정책을 적용 합니다. 그렇지 않은 경우 정책 불일치로 인해 IPsec/IKE VPN 터널이 연결 되지 않습니다.

   > [!IMPORTANT]
   > 연결에 IPsec/IKE 정책이 지정되고 나면 Azure VPN Gateway는 해당 특정 연결에 지정된 암호화 알고리즘 및 키 수준으로 된 IPsec/IKE 제안만 보내거나 수락합니다. 두 연결에 대한 IPsec 정책이 같은지 확인합니다. 그러지 않으면 VNet 간 연결이 설정되지 않습니다.

6. 이러한 단계를 완료 한 후 몇 분 후에 연결이 설정 되 고 다음과 같은 네트워크 토폴로지가 설정 됩니다.

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE 정책 다이어그램" border="false":::

## <a name="to-remove-custom-ipsecike-policy-from-a-connection"></a><a name ="deletepolicy"></a>연결에서 사용자 지정 IPsec/IKE 정책을 제거 하려면

1. 연결에서 사용자 지정 정책을 제거 하려면 연결 리소스로 이동 하 고 **구성** 페이지로 이동 하 여 현재 정책을 확인 합니다.

2. **IPsec/IKE 정책** 옵션에서 **기본값** 을 선택 합니다. 그러면 연결에 대해 이전에 지정 된 모든 사용자 지정 정책이 제거 되 고이 연결에 대 한 기본 IPsec/IKE 설정이 복원 됩니다.

   :::image type="content" source="./media/ipsec-ike-policy-howto/delete-policy.png" alt-text="IPsec/IKE 정책 다이어그램":::

3. **저장** 을 선택 하 여 사용자 지정 정책을 제거 하 고 연결에 대 한 기본 IPSEC/IKE 설정을 복원 합니다.

## <a name="next-steps"></a>다음 단계

정책 기반 트래픽 선택기에 대한 자세한 내용은 [여러 온-프레미스 정책 기반 VPN 디바이스 연결](vpn-gateway-connect-multiple-policybased-rm-ps.md)을 참조하세요.
