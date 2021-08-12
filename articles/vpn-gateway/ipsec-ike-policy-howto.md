---
title: 'S2S VPN 및 VNet 간 연결을 위한 IPsec/IKE 정책: Azure Portal'
titleSuffix: Azure VPN Gateway
description: Azure Portal을 사용하여 Azure VPN Gateways로 S2S 또는 VNet 간 연결에 대한 IPsec/IKE 정책을 구성하는 방법을 알아봅니다.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/28/2021
ms.author: yushwang
ms.openlocfilehash: 04dd5a1e8907d7314f9d96a81ae6965c943dc60c
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108229273"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections-azure-portal"></a>S2S VPN 또는 VNet 간 연결에 대한 IPsec/IKE 정책 구성: Azure Portal

이 문서에서는 Azure Portal을 사용하여 VPN Gateway 사이트 간 VPN 또는 VNet 간 연결에 대한 IPsec/IKE 정책을 구성하는 단계를 안내합니다. 다음 섹션에서는 IPsec/IKE 정책을 만들고 구성하고 이 정책을 새 연결 또는 기존 연결에 적용하는 방법을 설명합니다.

## <a name="about-ipsec-and-ike-policy-parameters"></a><a name="about"></a>IPsec 및 IKE 정책 매개 변수 정보

IPsec 및 IKE 프로토콜 표준은 다양하게 결합된 다양한 암호화 알고리즘을 지원합니다. 이러한 지원이 어떻게 프레미스 간 및 VNet 간 연결이 규정 준수 또는 보안 요구 사항을 충족하도록 하는 데 도움이 될 수 있는지를 확인하려면 [암호화 요구 사항 및 Azure VPN Gateway 정보](vpn-gateway-about-compliance-crypto.md)를 참조하세요.

이 문서에서는 IPsec/IKE 정책을 만들고 구성하여 새 연결 또는 기존 VPN Gateway 연결에 적용하기 위한 지침을 제공합니다.

### <a name="considerations"></a>고려 사항

* IPsec/IKE 정책은 다음 게이트웨이 SKU에만 작동합니다.
  * ***VpnGw1~5 및 VpnGw1AZ~5AZ***
  * ***Standard** _ 및 _ *_HighPerformance_**
* 지정된 연결에 대해 ***하나의*** 정책 조합만 지정할 수 있습니다.
* IKE(주 모드)와 IPsec(빠른 모드) 둘 다에 대한 모든 알고리즘 및 매개 변수를 지정해야 합니다. 부분 정책 지정은 허용되지 않습니다.
* 해당 VPN 디바이스 공급업체 사양을 참조하여 정책이 해당 온-프레미스 VPN 디바이스에서 지원되는지 확인하세요. 정책이 호환되지 않는 경우 S2S 또는 VNet 간 연결을 설정할 수 없습니다.

## <a name="workflow"></a><a name ="workflow"></a>워크플로

이 섹션에서는 S2S VPN 또는 VNet 간 연결에 대한 IPsec/IKE 정책을 만들고 업데이트하는 워크플로를 설명합니다.

1. 가상 네트워크 및 VPN 게이트웨이를 만듭니다.
2. 프레미스 간 연결에 대한 로컬 네트워크 게이트웨이 또는 VNet 간 연결에 대한 다른 가상 네트워크 및 게이트웨이를 만듭니다.
3. 연결(IPsec 또는 VNet2VNet)을 만듭니다.
4. 연결 리소스에 대한 IPsec/IKE 정책을 구성/업데이트/제거합니다.

이 문서의 지침은 다음 다이어그램에 표시된 대로 IPsec/IKE 정책을 설정하고 구성하는 데 도움이 됩니다.

:::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE 정책 다이어그램" border="false":::

## <a name="supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>지원되는 암호화 알고리즘 및 키 수준

### <a name="algorithms-and-keys"></a><a name ="table1"></a>알고리즘 및 키

다음 표에는 고객이 구성 가능하도록 지원되는 암호화 알고리즘 및 키 강도가 나와 있습니다.

| **IPsec/IKE**    | **Options**    |
| ---              | ---            |
| IKE 암호화   | AES256, AES192, AES128, DES3, DES                  |
| IKE 무결성    | SHA384, SHA256, SHA1, MD5                          |
| DH 그룹         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, 없음 |
| IPsec 암호화 | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, 없음    |
| IPsec 무결성  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS 그룹        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, 없음   |
| QM SA 수명   | (**선택 사항**: 지정되지 않으면 기본값이 사용됨)<br>초(정수, **최소 300**/기본값 27,000초)<br>KB(정수, **최소 1,024**/기본값 102,400,000KB)    |
| 트래픽 선택기 | UsePolicyBasedTrafficSelectors** ($True/$False - **선택 사항**, 지정되지 않으면 기본값 $False)    |
| DPD 제한 시간      | 초(정수: 최소 9/최대 3600, 기본 45초) |
|  |  |

#### <a name="important-requirements"></a>중요 요구 사항

* 온-프레미스 VPN 디바이스 구성은 Azure IPsec/IKE 정책에서 지정한 다음 알고리즘 및 매개 변수가 일치하거나 포함해야 합니다.
  * IKE 암호화 알고리즘(기본 모드/1단계)
  * IKE 무결성 알고리즘(기본 모드/1단계)
  * DH 그룹(기본 모드/1단계)
  * IPsec 암호화 알고리즘(빠른 모드/2단계)
  * IPsec 무결성 알고리즘(빠른 모드/2단계)
  * PFS 그룹(빠른 모드/2 단계)>    * 트래픽 선택기(UsePolicyBasedTrafficSelectors가 사용되는 경우)
  * SA 수명은 로컬 사양일 뿐이며 일치하지 않아도 됩니다.

* GCMAES가 IPsec 암호화 알고리즘에 사용되면 IPsec 무결성에 대해 동일한 GCMAES 알고리즘 및 키 길이를 선택해야 합니다(예: 둘 다에 대해 GCMAES128 사용).

* 위의 [알고리즘 및 키 테이블](#table1)에서 다음을 수행합니다.
  * IKE는 주 모드 또는 1단계에 해당합니다.
  * IPsec은 빠른 모드 또는 2단계에 해당합니다.
  * DH 그룹은 기본 모드 또는 1단계에서 사용되는 Diffie-Hellmen 그룹을 지정합니다.
  * PFS 그룹은 빠른 모드 또는 2단계에서 사용되는 Diffie-Hellmen 그룹을 지정했습니다.

* IKE 주 모드 SA 수명은 Azure VPN Gateways에서 28,800초로 고정됩니다.

* 연결에 대해 **UsePolicyBasedTrafficSelectors** 를 $True로 설정하면 온-프레미스의 정책 기반 VPN 방화벽에 연결되도록 Azure VPN Gateways가 구성됩니다. PolicyBasedTrafficSelectors를 사용하도록 설정한 경우 VPN 디바이스에 온-프레미스 네트워크(로컬 네트워크 게이트웨이) 접두사 및 Azure Virtual Network 접두사 간의 모든 조합으로 정의된 일치하는 트래픽 선택기가 있는지 확인해야 합니다. 예를 들어 온-프레미스 네트워크 접두사가 10.1.0.0/16 및 10.2.0.0/16이고 가상 네트워크 접두사가 192.168.0.0/16 및 172.16.0.0/16이면 다음 트래픽 선택기를 지정해야 합니다.
  * 10.1.0.0/16 <====> 192.168.0.0/16
  * 10.1.0.0/16 <====> 172.16.0.0/16
  * 10.2.0.0/16 <====> 192.168.0.0/16
  * 10.2.0.0/16 <====> 172.16.0.0/16

   정책 기반 트래픽 선택기에 대한 자세한 내용은 [여러 온-프레미스 정책 기반 VPN 디바이스 연결](vpn-gateway-connect-multiple-policybased-rm-ps.md)을 참조하세요.

* DPD 제한 시간 - 기본값은 Azure VPN Gateways에서 45초입니다. 시간 제한을 더 짧은 기간으로 설정하면 IKE가 더 적극적으로 키를 다시 입력하여 연결이 끊어진 것처럼 보이는 경우도 있습니다. 온-프레미스 위치가 VPN 게이트웨이가 있는 Azure 지역에서 멀리 떨어져 있거나 물리적 링크 조건으로 인해 패킷 손실이 발생할 수 있는 경우에는 적합하지 않을 수 있습니다. 일반적인 권장 사항은 제한 시간을 **30~45** 초 사이로 설정하는 것입니다.

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

## <a name="s2s-vpn-with-ipsecike-policy"></a><a name ="S2S"></a>IPsec/IKE 정책을 사용하는 S2S VPN

이 섹션에서는 IPsec/IKE 정책을 사용하여 사이트 간 VPN 연결을 만드는 단계를 안내합니다. 다음 단계에서는 아래 다이어그램과 같이 연결을 만듭니다.

:::image type="content" source="./media/ipsec-ike-policy-howto/site-to-site-diagram.png" alt-text="사이트 간 정책" border="false":::

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>1단계 - 가상 네트워크, VPN Gateway 및 로컬 네트워크 게이트웨이 만들기

아래 스크린샷과 같이 다음 리소스를 만듭니다. 단계는 [사이트 간 VPN 연결 만들기](./tutorial-site-to-site-portal.md)를 참조하세요.

* **가상 네트워크:**  TestVNet1

   :::image type="content" source="./media/ipsec-ike-policy-howto/testvnet-1.png" alt-text="VNet":::

* **VPN 게이트웨이:** VNet1GW

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-1-gateway.png" alt-text="게이트웨이":::

* **로컬 네트워크 게이트웨이:** Site6

   :::image type="content" source="./media/ipsec-ike-policy-howto/lng-site-6.png" alt-text="사이트":::

* **연결:** VNet1에서 Site6으로

    :::image type="content" source="./media/ipsec-ike-policy-howto/connection-site-6.png" alt-text="연결":::

### <a name="step-2---configure-ipsecike-policy-on-the-s2s-vpn-connection"></a><a name="s2sconnection"></a>2단계 - S2S VPN 연결에서 IPsec/IKE 정책 구성

이 섹션에서는 다음 알고리즘 및 매개 변수를 사용하여 IPsec/IKE 정책을 구성합니다.

* IKE:   AES256, SHA384, DHGroup24, DPD 제한 시간 45초
* IPsec: AES256, SHA256, PFS 없음, SA 수명 30000초 및 102400000KB

1. Azure Portal에서 연결 리소스 **VNet1toSite6** 으로 이동합니다. **구성** 페이지를 선택하고 **사용자 지정** IPsec/IKE 정책을 선택하여 모든 구성 옵션을 표시합니다. 아래 스크린샷은 목록에 따른 구성을 보여줍니다.

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-site-6.png" alt-text="Site6":::

1. IPsec으로 GCMAES를 사용하는 경우 IPsec 암호화 및 무결성 모두에 대해 동일한 GCMAES 알고리즘 및 키 길이를 사용해야 합니다. 예를 들어 아래 스크린샷은 IPsec 암호화와 IPsec 무결성 모두에 대해 GCMAES128을 지정합니다.

   :::image type="content" source="./media/ipsec-ike-policy-howto/gcmaes.png" alt-text="IPsec용 GCMAES":::

1. 위에서 설명한 대로 선택적으로 **정책 기반 트래픽 선택기 사용** 옵션에 대해 **사용** 을 선택하여 Azure VPN 게이트웨이가 온-프레미스의 정책 기반 VPN 디바이스에 연결할 수 있도록 할 수 있습니다.

   :::image type="content" source="./media/ipsec-ike-policy-howto/policy-based-selector.png" alt-text="정책 기반 트래픽 선택기":::

1. 모든 옵션을 선택한 후에는 **저장** 을 선택하여 연결 리소스에 대한 변경 내용을 커밋합니다. 정책은 약 1분 후에 적용됩니다.

> [!IMPORTANT]
>
> * 연결에 IPsec/IKE 정책이 지정되고 나면 Azure VPN Gateway는 해당 특정 연결에 지정된 암호화 알고리즘 및 키 수준으로 된 IPsec/IKE 제안만 보내거나 수락합니다. 연결에 대한 온-프레미스 VPN 디바이스에서 정확한 정책 조합을 사용하거나 수락하는지 확인합니다. 그러지 않으면 S2S VPN 터널이 설정되지 않습니다.
>
> * 위의 스크린샷과 같이 **정책 기반 트래픽 선택기** 및 **DPD 제한 시간** 옵션은 사용자 지정 IPsec/IKE 정책 없이 **기본** 정책으로 지정할 수 있습니다.
>

## <a name="vnet-to-vnet-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>IPsec/IKE 정책을 사용하는 VNet 간 연결

IPsec/IKE 정책을 사용하여 VNet 간 연결을 만드는 단계는 S2S VPN 연결을 만드는 단계와 유사합니다.

:::image type="content" source="./media/ipsec-ike-policy-howto/vnet-policy.png" alt-text="VNet 간 정책 다이어그램" border="false":::

1. [VNet 간 연결 만들기](vpn-gateway-vnet-vnet-rm-ps.md) 문서의 단계를 사용하여 VNet 간 연결을 만듭니다.

2. 단계를 완료하면 VNet2GW 리소스의 아래 스크린샷과 같이 VNet 간 연결 두 개가 표시됩니다.

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-connections.png" alt-text="VNet 간 연결":::

3. 연결 리소스로 이동하고 포털의 **구성** 페이지로 이동합니다. **IPsec/IKE 정책** 에서 **사용자 지정** 을 선택하여 사용자 지정 정책 옵션을 표시합니다. 해당 키 길이로 암호화 알고리즘을 선택합니다.

   스크린샷은 다음 알고리즘 및 매개 변수가 포함된 다른 IPsec/IKE 정책을 보여줍니다.
   * IKE: AES128, SHA1, DHGroup14, DPD 제한 시간 45초
   * IPsec: GCMAES128, GCMAES128, PFS14, SA 수명 14400초 및 102400000KB

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-policy.png" alt-text="연결 정책":::

4. **저장** 을 선택하여 연결 리소스에 대한 정책 변경 내용을 적용합니다.

5. 다른 연결 리소스인 VNet2toVNet1에 동일한 정책을 적용합니다. 그렇지 않으면 정책 불일치로 인해 IPsec/IKE VPN 터널이 연결되지 않습니다.

   > [!IMPORTANT]
   > 연결에 IPsec/IKE 정책이 지정되고 나면 Azure VPN Gateway는 해당 특정 연결에 지정된 암호화 알고리즘 및 키 수준으로 된 IPsec/IKE 제안만 보내거나 수락합니다. 두 연결에 대한 IPsec 정책이 같은지 확인합니다. 그러지 않으면 VNet 간 연결이 설정되지 않습니다.

6. 이러한 단계를 완료하고 나면 몇 분 후에 연결이 설정되고, 다음과 같은 네트워크 토폴로지가 구현됩니다.

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE 정책 다이어그램" border="false":::

## <a name="to-remove-custom-ipsecike-policy-from-a-connection"></a><a name ="deletepolicy"></a>연결에서 사용자 지정 IPsec/IKE 정책을 제거하려면

1. 연결에서 사용자 지정 정책을 제거하려면 연결 리소스로 이동하고 **구성** 페이지로 이동하여 현재 정책을 확인합니다.

2. **IPsec/IKE 정책** 옵션에서 **기본값** 을 선택합니다. 그러면 연결에 대해 이전에 지정된 모든 사용자 지정 정책이 제거되고 이 연결에 대한 기본 IPsec/IKE 설정이 복원됩니다.

   :::image type="content" source="./media/ipsec-ike-policy-howto/delete-policy.png" alt-text="정책 삭제":::

3. **저장** 을 선택하여 사용자 지정 정책을 제거하고 연결에 대한 기본 IPsec/IKE 설정을 복원합니다.

## <a name="next-steps"></a>다음 단계

정책 기반 트래픽 선택기에 대한 자세한 내용은 [여러 온-프레미스 정책 기반 VPN 디바이스 연결](vpn-gateway-connect-multiple-policybased-rm-ps.md)을 참조하세요.