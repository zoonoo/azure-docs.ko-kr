---
title: Azure Route Server에 대 한 질문과 대답
description: Azure Route Server에 대 한 자주 묻는 질문에 대 한 답변을 찾습니다.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 03/08/2021
ms.author: duau
ms.openlocfilehash: 6eed0ed3e936b0e9a534c82a3105c2ed37cab3d5
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485422"
---
# <a name="azure-route-server-preview-faq"></a>Azure Route Server (미리 보기) FAQ

> [!IMPORTANT]
> Azure Route Server(미리 보기)는 현재 퍼블릭 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="what-is-azure-route-server"></a>Azure Route Server 란?

Azure Route Server는 NVA (네트워크 가상 어플라이언스)와 가상 네트워크 간의 라우팅을 쉽게 관리할 수 있는 완전히 관리 되는 서비스입니다.

### <a name="is-azure-route-server-just-a-vm"></a>Azure 경로 서버는 VM만 있나요?

아니요. Azure Route Server는 고가용성을 사용 하 여 설계 된 서비스입니다. [가용성 영역](../availability-zones/az-overview.md)를 지 원하는 Azure 지역에 배포 된 경우 영역 수준 중복성을 갖게 됩니다.

### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Azure 경로 서버에서 지 원하는 라우팅 프로토콜은 무엇 인가요?

Azure 경로 서버는 BGP (Border Gateway Protocol)만 지원 합니다. NVA는 가상 네트워크의 전용 서브넷에 Azure 경로 서버를 배포 해야 하므로 다중 홉 외부 BGP를 지원 해야 합니다. 사용자가 선택 하는 [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) 은 NVA에서 BGP를 구성할 때 Azure 경로 서버에서 사용 하는 것과 달라 야 합니다.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Azure 경로 서버는 NVA와 내 Vm 간에 데이터 트래픽을 라우트 하나요?

아니요. Azure Route Server는 NVA와 BGP 경로만 교환 합니다. 데이터 트래픽은 NVA에서 선택한 VM으로 직접 이동 하 고 VM에서 NVA로 직접 이동 합니다.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-will-it-program-all-copies-of-the-route-but-each-with-a-different-next-hop-to-the-vms-in-the-virtual-network"></a>Azure Route Server가 둘 이상의 NVA와 동일한 경로를 수신 하는 경우에는 가상 네트워크의 Vm에 대 한 경로의 모든 복사본 (다른 다음 홉을 사용 하 여)을 프로그램 하나요?

예. 경로 길이가 경로 길이와 동일한 경우에만 가능 합니다. Vm이이 경로의 대상으로 트래픽을 보내는 경우 VM 호스트는 ECMP (다중 경로) 라우팅을 Equal-Cost 수행 합니다. 그러나 NVA 하나가 다른 Nva 보다 경로 길이가 짧은 경로를 보내는 경우 Azure Route Server는이 NVA로 설정 된 다음 홉이 가상 네트워크의 Vm으로 설정 된 경로만을 프로그래밍 합니다.

### <a name="does-azure-route-server-support-vnet-peering"></a>Azure 경로 서버에서 VNet 피어 링을 지원 하나요?

예. Azure 경로 서버를 호스트 하는 VNet을 다른 VNet에 피어 링 하는 경우 해당 VNet에서 원격 게이트웨이 사용을 사용 하도록 설정 합니다. Azure Route Server는 VNet의 주소 공간을 알아보고 모든 피어 링 Nva에 보냅니다.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>어떤 ASNs (자치 시스템 번호)를 사용할 수 있나요?

네트워크 가상 어플라이언스에서 고유한 공용 ASNs 또는 개인 ASNs를 사용할 수 있습니다. Azure 또는 IANA에서 예약한 범위는 사용할 수 없습니다.
다음 ASNs는 Azure 또는 IANA에서 예약됩니다.

* Azure에서 예약된 ASN:
    * 공용 ASN: 8074, 8075, 12076
    * 프라이빗 ASN: 65515, 65517, 65518, 65519, 65520
* [IANA에서 예약한](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) ASN:
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>32 비트 (4 바이트) ASNs를 사용할 수 있나요?

아니요, Azure 경로 서버는 16 비트 (2 바이트)의 ASNs만 지원 합니다.

## <a name="route-server-limits"></a><a name = "limitations"></a>경로 서버 제한

Azure 경로 서버에는 다음과 같은 제한 사항이 있습니다 (배포 당).

| 리소스 | 제한 |
|----------|-------|
| 지원 되는 BGP 피어 수 | 8 |
| 각 BGP 피어에서 Azure 경로 서버에 알릴 수 있는 경로 수 | 200 |
| Azure Route Server가 Express 경로 또는 VPN gateway에 보급할 수 있는 경로 수 | 200 |

NVA가 제한 보다 많은 경로를 보급 하는 경우 BGP 세션이 삭제 됩니다. 게이트웨이 및 Azure 경로 서버에 발생 하는 경우 온-프레미스 네트워크에서 Azure로의 연결을 잃게 됩니다. 자세한 내용은 [Azure 가상 컴퓨터 라우팅 문제 진단](../virtual-network/diagnose-network-routing-problem.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure Route Server를 구성](quickstart-configure-route-server-powershell.md)하는 방법에 대해 알아봅니다.
