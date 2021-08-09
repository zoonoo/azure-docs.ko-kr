---
title: Azure 가상 네트워크의 공용 IPv6 주소 접두사
titlesuffix: Azure Virtual Network
description: Azure 가상 네트워크의 공용 IPv6 주소 접두사에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 3e3d3c01ba1190871da39ce27de6a95952d09be1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99538209"
---
# <a name="reserved-public-ipv6-address-prefix"></a>예약된 공용 IPv6 주소 접두사

Azure에서 이중 스택(IPv4+IPv6) VNet(가상 네트워크) 및 VM(가상 머신)은 인터넷 연결이 없기 때문에 기본적으로 안전합니다. Azure에서 가져온 공용 IPv6 주소를 사용하여 Azure Load Balancer 및 VM에 IPv6 인터넷 연결을 쉽게 추가할 수 있습니다.

예약한 모든 공용 IP는 선택한 Azure 지역 및 Azure 구독과 연결됩니다. 구독의 Azure Load Balancer 또는 VM 간에 예약된(고정) IPv6 공용 IP를 이동할 수 있습니다. IPv6 공용 IP를 완전히 분리할 수 있으며 준비가 되면 사용할 수 있도록 유지됩니다.

> [!WARNING]
> 실수로 공용 IP 주소를 삭제하지 않도록 주의합니다. 공용 IP를 삭제하면 구독에서 제거되며 복구할 수 없습니다(Azure 지원의 도움을 받아도 안 됨).

개별 IPv6 주소를 예약하는 것 외에도, 사용할 Azure IPv6 주소의 연속 범위(또는 IP 접두사)를 예약할 수 있습니다.  개별 IP 주소와 마찬가지로 예약된 접두사는 선택한 Azure 지역 및 Azure 구독과 연결됩니다. 예측 가능하고 연속적인 주소 범위를 예약하는 것은 많은 용도가 있습니다. 예를 들어 고정 IP 범위를 온-프레미스 방화벽에 쉽게 프로그래밍할 수 있으므로 회사와 고객이 Azure에서 호스트하는 애플리케이션의 IP *필터링* 을 크게 단순화할 수 있습니다.  필요에 따라 IP 접두사에서 개별 공용 IP를 만들 수 있으며 이러한 개별 공용 IP를 삭제하면 나중에 다시 사용할 수 있도록 예약된 범위로 *반환* 됩니다. IP 접두사에 있는 모든 IP 주소는 접두사를 삭제할 때까지 독점적으로 사용하도록 예약되어 있습니다.



## <a name="ipv6-prefix-sizes"></a>IPv6 접두사 크기
다음 공용 IP 접두사 크기를 사용할 수 있습니다.

-  최소 IPv6 접두사 크기: /127 = 2개 주소
-  최대 IPv6 접두사 크기: /124 = 16개 주소

접두사 크기는 CIDR(Classless Inter-Domain Routing) 마스크 크기로 지정됩니다. 예를 들어 /128의 마스크는 IPv6 주소가 128비트로 구성되어 있으므로 개별 IPv6 주소를 나타냅니다.

## <a name="pricing"></a>가격 책정
 
Azure 공용 IP(개별 IP 주소 및 IP 범위) 사용과 관련된 비용은 [공용 IP 주소 가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses/)을 참조하세요.

## <a name="limitations"></a>제한 사항
IPv6은 Azure에서 애플리케이션(VM 또는 부하 분산 장치)을 삭제하고 다시 배포하는 경우 IPv6 주소가 변경됨을 의미하는 "동적" 할당이 있는 기본 공용 IP에서만 지원됩니다. 표준 IPv6 공용 IP는 표준 내부 Load Balancer가 할당된 서브넷 내에서 동적 할당을 지원할 수도 있지만 고정(예약) 할당만 지원합니다.  

모범 사례로 IPv6 애플리케이션에 표준 공용 IP 및 표준 Load Balancer를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
- 공용 [IPv6 주소 접두사](ipv6-reserve-public-ip-address-prefix.md)를 예약합니다.
- [IPv6 주소](ipv6-overview.md)에 대해 자세히 알아보세요.
- Azure에서 [공용 IP를 만들고 사용하는 방법](virtual-network-public-ip-address.md)(IPv4 및 IPv6 모두)에 대해 알아봅니다.
