---
title: 가상 WAN 지점 및 사이트 간 IPsec 정책
titleSuffix: Azure Virtual WAN
description: Azure 가상 WAN 지점 및 사이트 간 IPsec 연결 정책에 대해 알아봅니다.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/24/2021
ms.author: wellee
ms.openlocfilehash: d64748bf7719af52b76c5e4141bfbbabe80bbae2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745909"
---
# <a name="point-to-site-ipsec-policies"></a>지점 및 사이트 간 IPsec 정책

이 문서에서는 Azure 가상 WAN의 지점 및 사이트 간 VPN 연결에 대해 지원 되는 IPsec 정책 조합을 보여 줍니다.

## <a name="default-ipsec-policies"></a>기본 IPsec 정책

다음 표에서는 지점 및 사이트 간 VPN 연결에 대 한 기본 IPsec 매개 변수를 보여 줍니다. 이러한 매개 변수는 지점 및 사이트 간 프로필을 만들 때 가상 WAN 지점 및 사이트 간 VPN gateway에서 자동으로 선택 됩니다.

| 설정 | 매개 변수 |
|--- |--- |
| 1 단계 IKE 암호화 | AES256 |
| 1 단계 IKE 무결성 |  SHA256 |
| DH 그룹 | DHGroup24 |
| 2 단계 IPsec 암호화 | GCMAES256|
| 2 단계 IPsec 무결성 | GCMAES25 |
| PFS 그룹 |PFS24|

## <a name="custom-ipsec-policies"></a>사용자 지정 IPsec 정책

사용자 지정 IPsec 정책으로 작업 하는 경우 다음 요구 사항을 염두에 두어야 합니다.

* **Ike** -1 단계 ike의 경우 ike 암호화에서 매개 변수를 선택할 수 있습니다. 또한 ike 무결성의 매개 변수와 DH 그룹의 매개 변수를 선택할 수 있습니다.
* **Ipsec** -2 단계 ipsec의 경우 ipsec 암호화의 매개 변수와 ipsec 무결성의 매개 변수 및 PFS를 선택할 수 있습니다. Ipsec 암호화 또는 IPsec 무결성에 대 한 매개 변수가 GCM 인 경우 IPsec 암호화 및 무결성 모두 동일한 알고리즘을 사용 해야 합니다. 예를 들어 IPsec 암호화를 위해 GCMAES128를 선택한 경우 IPsec 무결성을 위해 GCMAES128도 선택 해야 합니다.  

다음 표에서는 지점 및 사이트 간 VPN 연결에 사용 가능한 IPsec 매개 변수를 보여 줍니다.

| 설정 | 매개 변수 |
|--- |--- |
| 1 단계 IKE 암호화 | AES128, AES256, GCMAES128, GCMAES256 |
| 1 단계 IKE 무결성 |  SHA256, SHA384 |
| DH 그룹 | DHGroup14, DHGroup24, ECP256, ECP384 |
| 2 단계 IPsec 암호화 | GCMAES128, GCMAES256, SHA256|
| 2 단계 IPsec 무결성 | GCMAES128, GCMAES256 |
| PFS 그룹 |PFS14, PFS24, ECP256, ECP384|

## <a name="next-steps"></a>다음 단계

[지점 및 사이트 간 연결을 만드는 방법](virtual-wan-point-to-site-portal.md) 을 참조 하세요.

가상 WAN에 대한 자세한 내용은 [가상 WAN FAQ](virtual-wan-faq.md)를 참조하세요.
