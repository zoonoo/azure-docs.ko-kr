---
title: Azure VPN 게이트웨이 메트릭에 대한 경고 설정
description: VPN 게이트웨이 메트릭에 대한 경고를 구성하는 단계
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: d57663f683ba4e2107ec6813a19fac7b2dcdd26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67605223"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>VPN 게이트웨이 메트릭에 대한 경고 설정

이 문서에서는 Azure VPN 게이트웨이 메트릭에 대한 경고를 설정하는 데 도움이 됩니다. Azure 모니터는 Azure 리소스에 대한 경고를 설정하는 기능을 제공합니다. "VPN" 유형의 가상 네트워크 게이트웨이에 대한 경고를 설정할 수 있습니다.


|**메트릭**   | **단위** | **세분성** | **설명** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 바이트/s  | 5분| 게이트웨이의 모든 사이트 간 연결의 평균 대역폭 사용률입니다.     |
|**P2SBandwidth**| 바이트/s  | 1분  | 게이트웨이의 모든 지점 간 연결의 평균 대역폭 사용률입니다.    |
|**P2SConnectionCount**| 개수  | 1분  | 게이트웨이에서 지점 간 연결 수를 계산합니다.   |
|**TunnelAverageBandwidth** | 바이트/s    | 5분  | 게이트웨이에서 생성된 터널의 평균 대역폭 사용률입니다. |
|**TunnelEgressBytes** | 바이트 | 5분 | 게이트웨이에서 생성된 터널의 나가는 트래픽입니다.   |
|**TunnelEgressPackets** | 개수 | 5분 | 게이트웨이에서 생성된 터널에서 나가는 패킷 수입니다.   |
|**TunnelEgressPacketDropTSMismatch** | 개수 | 5분 | 트래픽 선택기 불일치로 인해 터널에서 삭제된 나가는 패킷 수입니다. |
|**TunnelIngressBytes** | 바이트 | 5분 | 게이트웨이에서 생성된 터널의 들어오는 트래픽입니다.   |
|**TunnelIngressPackets** | 개수 | 5분 | 게이트웨이에서 생성된 터널에서 들어오는 패킷 수입니다.   |
|**TunnelIngressPacketDropTSMismatch** | 개수 | 5분 | 트래픽 선택기 불일치로 인해 터널에서 삭제된 들어오는 패킷 수입니다. |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Azure 포털을 사용하여 메트릭을 기반으로 Azure Monitor 경고 설정

다음 예제 단계는 게이트웨이에서 다음에 대한 경고를 만듭니다.

- **메트릭:** 터널 평균 대역폭
- **조건:** 대역폭 > 초당 10바이트
- **창:** 5분
- **경고 작업:** 메일 주소



1. 가상 네트워크 게이트웨이 리소스로 이동하여 **모니터링** 탭에서 **경고를 선택합니다.** 그런 다음 새 경고 규칙을 만들거나 기존 경고 규칙을 편집합니다.

   ![경고 규칙을 만들기 위한 선택](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "생성")

2. VPN 게이트웨이를 리소스로 선택합니다.

   ![리소스 목록의 선택 버튼 과 VPN 게이트웨이](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "선택")

3. 경고를 구성할 메트릭을 선택합니다.

   ![메트릭 목록에서 선택한 메트릭](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "선택")
4. 신호 논리를 구성합니다. 세 가지 구성 요소가 있습니다.

    a. **측정기준:** 측정값에 차원이 있는 경우 경고가 해당 차원의 데이터만 평가되도록 특정 차원 값을 선택할 수 있습니다. 이는 선택 사항입니다.

    b. **조건**: 메트릭 값을 평가하는 작업입니다.

    다. **시간**: 메트릭 데이터의 세분성 및 경고를 평가할 기간을 지정합니다.

   ![신호 논리 구성에 대한 세부 정보](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "선택")

5. 구성된 규칙을 보려면 경고 **규칙 관리를**선택합니다.

   ![경고 규칙 관리 버튼](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "선택")

## <a name="next-steps"></a>다음 단계

터널 진단 로그에 대한 경고를 구성하려면 [VPN 게이트웨이 진단 로그에 대한 경고 설정을](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)참조하십시오.
