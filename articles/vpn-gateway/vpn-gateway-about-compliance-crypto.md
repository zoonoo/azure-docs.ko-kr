---
title: 'Azure VPN 게이트웨이: 암호화 요구 사항'
description: 이 문서에서는 암호화 요구 사항 및 Azure VPN Gateway에 대해 알아봅니다.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: 93e13592d9c434b159ad4f4c10ef30328941c64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902835"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>암호화 요구 사항 및 Azure VPN Gateway 정보

이 문서에서는 Azure 내에서 크로스-프레미스 S2S VPN 터널 및 VNet 간 연결 모두에 대한 암호화 요구 사항을 충족하도록 Azure VPN Gateway를 구성하는 방법을 설명합니다.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Azure VPN 연결용 IKEv1 및 IKEv2 정보

전통적으로 기본 SCO에 대해서만 IKEv1 연결을 허용했으며 기본 SCO를 제외한 모든 VPN 게이트웨이 SCO에 대해 IKEv2 연결을 허용했습니다. 기본 SCO는 단 1개의 연결만 허용하며 성능과 같은 다른 제한 사항과 함께 IKEv1 프로토콜만 지원하는 레거시 장치를 사용하는 고객은 경험이 제한적이었습니다. IKEv1 프로토콜을 사용하는 고객의 경험을 향상시키기 위해 이제 기본 SKU를 제외한 모든 VPN 게이트웨이 SKU에 대해 IKEv1 연결을 허용하고 있습니다. 자세한 내용은 [VPN 게이트웨이 SCO를](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku)참조하십시오.

![Azure VPN 게이트웨이 IKEv1 및 IKEv2 연결](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

IKEv1 및 IKEv2 연결이 동일한 VPN 게이트웨이에 적용되면 이 두 연결 간의 전송이 자동으로 활성화됩니다.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Azure VPN Gateway에 대한 IPsec 및 IKE 정책 매개 변수 정보

IPsec 및 IKE 프로토콜 표준은 다양하게 결합된 다양한 암호화 알고리즘을 지원합니다. 암호화 알고리즘과 매개 변수의 특정 조합을 요청하지 않는 경우 Azure VPN 게이트웨이는 기본 제안서 집합을 사용합니다. 기본 정책 집합은 기본 구성에서 광범위한 타사 VPN 디바이스와의 상호 운용성을 극대화하기 위해 선택되었습니다. 따라서 정책 및 제안 수에서 사용 가능한 암호화 알고리즘 및 키 길이의 가능한 모든 조합을 다룰 수는 없습니다.

Azure VPN 게이트웨이에 대한 기본 정책 설정은 문서에 나열되어 [있습니다.](vpn-gateway-about-vpn-devices.md)

## <a name="cryptographic-requirements"></a>암호화 요구 사항

일반적으로 규정 준수 또는 보안 요구 사항으로 인해 특정 암호화 알고리즘 또는 매개 변수가 필요한 통신의 경우 Azure 기본 정책 집합이 아닌 특정 암호화 알고리즘 및 키 장점을 사용하여 사용자 지정 IPsec/IKE 정책을 사용하도록 Azure VPN 게이트웨이를 구성할 수 있습니다.

예를 들어 Azure VPN 게이트웨이의 IKEv2 기본 모드 정책은 Diffie-Hellman 그룹 2(1024 비트)만 사용하는 반면, 그룹 14(2048비트), 그룹 24(2048비트 MODP 그룹) 또는 ECP(타원형 곡선)와 같이 IKE에서 사용할 더 강력한 그룹을 지정해야 할 수 있습니다. 그룹) 256 또는 384 비트(그룹 19 및 그룹 20). 유사한 요구 사항은 IPsec 빠른 모드 정책에도 적용됩니다.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Azure VPN Gateway의 사용자 지정 IPsec/IKE 정책

이제 Azure VPN Gateway에서 연결별 사용자 지정 IPsec/IKE 정책을 지원합니다. 아래 예제에 나와 있는 것처럼 사이트 간 또는 VNet 간 연결에 대해 원하는 키 강도를 가진 IPsec 및 IKE에 대한 특정 암호화 알고리즘 조합을 선택할 수 있습니다.

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

IPsec/IKE 정책을 만들어 새 연결 또는 기존 연결에 적용할 수 있습니다.

### <a name="workflow"></a>워크플로

1. 다른 방법 문서에 설명된 대로 연결 토폴로지에 대한 가상 네트워크, VPN Gateway 또는 로컬 네트워크 게이트웨이 만들기
2. IPsec/IKE 정책 만들기
3. S2S 또는 VNet 간 연결을 만들 때 정책을 적용할 수 있습니다.
4. 연결이 이미 생성된 경우 기존 연결에 정책을 적용하거나 업데이트할 수 있음

## <a name="ipsecike-policy-faq"></a>IPsec/IKE 정책 FAQ

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>다음 단계

연결에 대한 사용자 지정 IPsec/IKE 정책을 구성하는 방법에 대한 단계별 지침은 [IPsec/IKE 정책 구성](vpn-gateway-ipsecikepolicy-rm-powershell.md)을 참조하세요.

또한 UsePolicyBasedTrafficSelectors 옵션에 대한 자세한 내용은 [여러 정책 기반 VPN 디바이스 연결](vpn-gateway-connect-multiple-policybased-rm-ps.md)을 참조하세요.
