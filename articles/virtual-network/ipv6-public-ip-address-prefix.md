---
title: Azure 가상 네트워크의 공용 IPv6 주소 접두사
titlesuffix: Azure Virtual Network
description: Azure 가상 네트워크에서 공용 IPv6 주소 접두사에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 8254a7d86d5cadc2ddc03940f4ab2d08de74bd86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72965167"
---
# <a name="reserved-public-ipv6-address-prefix-preview"></a>예약된 공용 IPv6 주소 접두사(미리 보기)

Azure에서는 인터넷에 연결되지 않으므로 이중 스택(IPv4+IPv6) 가상 네트워크(VNet) 및 가상 시스템(VM)은 기본적으로 안전합니다. Azure에서 가져온 공용 IPv6 주소를 사용하여 Azure 로드 밸런서 및 VM에 IPv6 인터넷 연결을 쉽게 추가할 수 있습니다.

예약하는 모든 공용 IP는 선택한 Azure 지역 및 Azure 구독과 연결됩니다. 구독의 Azure 로드 밸런서 또는 VM 간에 예약된(정적) IPv6 공용 IP를 이동할 수 있습니다. IPv6 공용 IP를 완전히 분리할 수 있으며 준비가 되면 사용할 수 있습니다.

> [!WARNING]
> 공용 IP 주소를 실수로 삭제하지 않도록 주의하십시오. 공용 IP를 삭제하면 구독에서 제거되며 Azure 지원의 도움으로도 복구할 수 없습니다.

개별 IPv6 주소를 예약하는 것 외에도 사용할 수 있도록 연속범위의 Azure IPv6 주소(IP 접두사라고 함)를 예약할 수 있습니다.  개별 IP 주소와 마찬가지로 예약된 접두사는 선택한 Azure 지역 및 Azure 구독과 연결됩니다. 예측 가능하고 연속적인 범위의 주소를 예약하는 것은 여러 가지 용도로 사용됩니다. 예를 들어 정적 IP 범위를 온-프레미스 방화벽으로 쉽게 프로그래밍할 수 있으므로 회사 및 고객의 Azure 호스팅 응용 프로그램의 IP *허용 목록(IP 허용 목록)을* 크게 간소화할 수 있습니다.  필요에 따라 IP 접두사에서 개별 공용 IP를 만들 수 있으며 이러한 개별 공용 IP를 삭제하면 나중에 다시 사용할 수 있도록 예약된 범위로 *반환됩니다.* IP 접두사에 있는 모든 IP 주소는 접두사를 삭제할 때까지 독점적으로 사용할 수 있습니다.

> [!Important]
> Azure 가상 네트워크에 대한 IPv6는 현재 공개 미리 보기 상태입니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="ipv6-prefix-sizes"></a>IPv6 접두사 크기
다음과 같은 공용 IP 접두사 크기를 사용할 수 있습니다.

-  최소 IPv6 접두사 크기: /127 = 2개의 주소
-  최대 IPv6 접두사 크기: /124 = 주소 16개

접두사 크기는 클래스리스 도메인 간 라우팅(CIDR) 마스크 크기로 지정됩니다. 예를 들어 /128의 마스크는 IPv6 주소가 128비트로 구성됨에 따라 개별 IPv6 주소를 나타냅니다.

## <a name="pricing"></a>가격 책정
 
개별 IP 주소와 IP 범위 모두 Azure 공용 IP 사용과 관련된 비용은 [공용 IP 주소 가격 책정을](https://azure.microsoft.com/pricing/details/ip-addresses/)참조하십시오.

## <a name="limitations"></a>제한 사항
IPv6는 Azure에서 응용 프로그램(VM 또는 로드 밸런서)을 삭제하고 다시 배포하는 경우 IPv6 주소가 변경된다는 것을 의미하는 "동적" 할당을 통해서만 기본 공용 IP에서 지원됩니다. 표준 IPv6 Public IP의 지원은 전적으로 정적(예약된) 할당이지만 표준 내부 로드 밸런서는 할당된 서브넷 내에서 동적 할당을 지원할 수도 있습니다.  

가장 좋은 방법은 IPv6 응용 프로그램에 표준 공용 IP 및 표준 로드 밸런서를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
- 공용 [IPv6 주소 접두사를 예약합니다.](ipv6-reserve-public-ip-address-prefix.md)
- [IPv6 주소에](ipv6-overview.md)대해 자세히 알아보십시오.
- Azure에서 공용 IP(IPv4 및 IPv6 모두)를 [만들고 사용하는 방법에](virtual-network-public-ip-address.md) 대해 알아봅니다.
