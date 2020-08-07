---
title: 'Azure VPN Gateway: 암호화 요구 사항'
description: Azure 내에서 크로스-프레미스 S2S VPN 터널 및 VNet 간 연결에 대 한 암호화 요구 사항을 충족 하도록 Azure VPN gateway를 구성 하는 방법에 대해 알아봅니다.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: b591baa3fd942eb46cc7351a15c8849c44fe0d12
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926964"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>암호화 요구 사항 및 Azure VPN Gateway 정보

이 문서에서는 Azure 내에서 크로스-프레미스 S2S VPN 터널 및 VNet 간 연결 모두에 대한 암호화 요구 사항을 충족하도록 Azure VPN Gateway를 구성하는 방법을 설명합니다.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Azure VPN 연결에 대 한 IKEv1 및 IKEv2 정보

일반적으로 기본 sku에 대해서만 IKEv1 연결을 허용 하 고 기본 Sku 이외의 모든 VPN gateway Sku에 대해 허용 되는 IKEv2 연결을 허용 했습니다. 기본 Sku는 1 개의 연결 및 성능 등의 기타 제한 사항을 허용 하 고, IKEv1 프로토콜만 지 원하는 레거시 장치를 사용 하는 고객은 제한 된 환경을 보유 하 고 있습니다. 이제 IKEv1 프로토콜을 사용 하는 고객의 경험을 향상 시키기 위해 기본 SKU를 제외 하 고 모든 VPN gateway Sku에 대 한 IKEv1 연결을 허용 합니다. 자세한 내용은 [VPN Gateway sku](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku)를 참조 하세요.

![Azure VPN Gateway IKEv1 및 IKEv2 연결](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

IKEv1 및 IKEv2 연결이 동일한 VPN gateway에 적용 되는 경우 이러한 두 연결 간의 전송은 자동으로 설정 됩니다.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Azure VPN Gateway에 대한 IPsec 및 IKE 정책 매개 변수 정보

IPsec 및 IKE 프로토콜 표준은 다양하게 결합된 다양한 암호화 알고리즘을 지원합니다. 암호화 알고리즘과 매개 변수의 특정 조합을 요청 하지 않으면 Azure VPN 게이트웨이에서 기본 제안 집합을 사용 합니다. 기본 정책 집합은 기본 구성에서 광범위한 타사 VPN 디바이스와의 상호 운용성을 극대화하기 위해 선택되었습니다. 따라서 정책 및 제안 수에서 사용 가능한 암호화 알고리즘 및 키 길이의 가능한 모든 조합을 다룰 수는 없습니다.

Azure VPN gateway에 대 한 기본 정책 집합은 [사이트 간 VPN Gateway 연결에 대 한 vpn 장치 및 IPsec/IKE 매개 변수 정보](vpn-gateway-about-vpn-devices.md)문서에 나열 되어 있습니다.

## <a name="cryptographic-requirements"></a>암호화 요구 사항

일반적으로 규정 준수 또는 보안 요구 사항으로 인해 특정 암호화 알고리즘 또는 매개 변수를 요구 하는 통신의 경우, 이제 Azure 기본 정책 집합 대신 특정 암호화 알고리즘 및 키 강도로 사용자 지정 IPsec/IKE 정책을 사용 하도록 Azure VPN gateway를 구성할 수 있습니다.

예를 들면 Azure VPN gateway에 대 한 IKEv2 기본 모드 정책은 Diffie-hellman 그룹 2 (1024 비트)만 사용 하는 반면, IKE에서 사용할 더 강력한 그룹 (예: 그룹 14 (2048 비트), 그룹 24 (2048-비트 MODP 그룹) 또는 ECP (타원 curve groups) 256 또는 384 비트 (각각 19 및 Group 20))을 지정 해야 할 수도 있습니다. 유사한 요구 사항은 IPsec 빠른 모드 정책에도 적용됩니다.

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
