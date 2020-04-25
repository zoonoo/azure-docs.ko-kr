---
title: Azure VPN Gateway 메트릭에 대 한 경고 설정
description: VPN Gateway 메트릭에 대 한 경고를 구성 하는 단계
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 01704030ee3142cac9a328e1a3edc024651919a0
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127901"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>VPN Gateway 메트릭에 대 한 경고 설정

이 문서는 Azure VPN Gateway 메트릭에 대 한 경고를 설정 하는 데 도움이 됩니다. Azure Monitor은 Azure 리소스에 대 한 경고를 설정 하는 기능을 제공 합니다. "VPN" 유형의 가상 네트워크 게이트웨이에 대 한 경고를 설정할 수 있습니다.


|**메트릭**   | **단위** | **세분성** | **설명** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 바이트/초  | 5분| 게이트웨이에서 모든 사이트 간 연결의 평균 총 대역폭 사용률입니다.     |
|**P2SBandwidth**| 바이트/초  | 1분  | 게이트웨이에서 모든 지점 및 사이트 간 연결의 평균 결합 된 대역폭 사용률입니다.    |
|**P2SConnectionCount**| 개수  | 1분  | 게이트웨이에서 지점 및 사이트 간 연결 수입니다.   |
|**TunnelAverageBandwidth** | 바이트/초    | 5분  | 게이트웨이에서 생성 된 터널의 평균 대역폭 사용률입니다. |
|**TunnelEgressBytes** | 바이트 | 5분 | 게이트웨이에서 생성 된 터널의 나가는 트래픽   |
|**TunnelEgressPackets** | 개수 | 5분 | 게이트웨이에서 만든 터널의 나가는 패킷 수입니다.   |
|**TunnelEgressPacketDropTSMismatch** | 개수 | 5분 | 트래픽 선택기 불일치로 인해 발생 하는 터널에 삭제 된 나가는 패킷 수입니다. |
|**TunnelIngressBytes** | 바이트 | 5분 | 게이트웨이에서 생성 된 터널의 수신 트래픽   |
|**TunnelIngressPackets** | 개수 | 5분 | 게이트웨이에서 생성 된 터널의 수신 패킷 수입니다.   |
|**TunnelIngressPacketDropTSMismatch** | 개수 | 5분 | 트래픽 선택기 불일치로 인해 발생 하는 터널에 대해 삭제 된 들어오는 패킷의 수입니다. |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Azure Portal를 사용 하 여 메트릭에 따라 Azure Monitor 경고 설정

다음 예제 단계는에 대 한 게이트웨이에 대 한 경고를 만듭니다.

- **메트릭:** TunnelAverageBandwidth
- **조건:** 대역폭 > 10 바이트/초
- **창:** 5 분
- **경고 동작:** 메일 주소



1. 가상 네트워크 게이트웨이 리소스로 이동 하 여 **모니터링** 탭에서 **경고** 를 선택 합니다. 그런 다음 새 경고 규칙을 만들거나 기존 경고 규칙을 편집 합니다.

   ![경고 규칙을 만들기 위한 선택 항목](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "생성")

2. VPN gateway를 리소스로 선택 합니다.

   ![리소스 목록의 선택 단추 및 VPN gateway](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "선택")

3. 경고에 대해 구성할 메트릭을 선택 합니다.

   ![메트릭 목록에서 선택한 메트릭](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "선택")
4. 신호 논리를 구성 합니다. 여기에는 세 가지 구성 요소가 있습니다.

    a. **차원**: 메트릭에 차원이 있는 경우 경고가 해당 차원의 데이터만 평가 하도록 특정 차원 값을 선택할 수 있습니다. 이러한 옵션은 선택 사항입니다.

    b. **Condition**: 메트릭 값을 평가 하는 작업입니다.

    c. **시간**: 메트릭 데이터의 세분성과 경고를 평가할 기간을 지정 합니다.

   ![신호 논리를 구성 하는 방법에 대 한 세부 정보](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "선택")

5. 구성 된 규칙을 보려면 **경고 규칙 관리**를 선택 합니다.

   ![경고 규칙 관리 단추](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "선택")

## <a name="next-steps"></a>다음 단계

터널 리소스 로그에 대 한 경고를 구성 하려면 [VPN Gateway 리소스 로그에 대 한 경고 설정](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)을 참조 하세요.
