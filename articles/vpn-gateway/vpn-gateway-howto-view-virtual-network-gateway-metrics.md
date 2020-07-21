---
title: Azure VPN Gateway 메트릭 보기
description: VPN Gateway 메트릭을 확인 하는 단계
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/12/2020
ms.author: alzam
ms.openlocfilehash: b3a79b8101a55eaf401c20cb118be3b0796b7aca
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531617"
---
# <a name="view-vpn-gateway-metrics"></a>VPN Gateway 메트릭 보기

Azure Monitor를 사용 하 여 Azure VPN 게이트웨이를 모니터링할 수 있습니다. 이 문서에서는 포털을 통해 사용할 수 있는 메트릭에 대해 설명 합니다. 메트릭은 간단한 실시간 시나리오를 지원 하 여 경고 및 빠른 문제 검색에 유용 하 게 사용할 수 있습니다.


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

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>다음 단계를 통해 메트릭을 찾고 볼 수 있습니다.

1. 포털에서 가상 네트워크 게이트웨이 리소스로 이동 합니다.
2. **개요** 를 선택 하 여 총 터널 수신 및 송신 메트릭을 확인 합니다.

   ![경고 규칙을 만들기 위한 선택 항목](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/overview.png "View")

3. 위에 나열 된 다른 메트릭을 볼 수 있습니다. 가상 네트워크 게이트웨이 리소스 아래의 **메트릭** 섹션을 클릭 하 고 드롭다운 목록에서 메트릭을 선택 합니다.

   ![리소스 목록의 선택 단추 및 VPN gateway](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/metrics.png "선택")

## <a name="next-steps"></a>다음 단계

터널 메트릭에 대 한 경고를 구성 하려면 [VPN Gateway 메트릭에 대 한 경고 설정](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)을 참조 하세요.

터널 리소스 로그에 대 한 경고를 구성 하려면 [VPN Gateway 리소스 로그에 대 한 경고 설정](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)을 참조 하세요.
