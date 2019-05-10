---
title: Azure VPN Gateway 메트릭에 대 한 경고 설정
description: VPN Gateway 메트릭에 대해 경고를 구성 하는 단계
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: e54dadbda0582095e8152ea30376d369177bfd86
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65509901"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>VPN Gateway 메트릭에 대 한 경고 설정

이 문서에서는 Azure VPN Gateway 메트릭에 대 한 경고를 설정 합니다. Azure Monitor는 Azure 리소스에 대 한 경고를 설정 하는 기능을 제공 합니다. "VPN" 형식의 가상 네트워크 게이트웨이에 대 한 경고를 설정할 수 있습니다.


|**메트릭**   | **단위** | **세분성** | **설명** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 바이트/초  | 5분| 게이트웨이의 모든 사이트 간 연결의 결합 된 대역폭 사용률을 평균입니다.     |
|**P2SBandwidth**| 바이트/초  | 1분  | 게이트웨이의 모든 지점-사이트 간 연결의 결합 된 대역폭 사용률을 평균입니다.    |
|**P2SConnectionCount**| 카운트  | 1분  | 게이트웨이에 지점-사이트 간 연결의 수입니다.   |
|**TunnelAverageBandwidth** | 바이트/초    | 5분  | 게이트웨이에서 터널의 평균 대역폭 사용률입니다. |
|**TunnelEgressBytes** | 바이트 | 5분 | 게이트웨이에서 터널에서 나가는 트래픽을 합니다.   |
|**TunnelEgressPackets** | 카운트 | 5분 | 게이트웨이에서 터널에서 나가는 패킷의 수입니다.   |
|**TunnelEgressPacketDropTSMismatch** | 카운트 | 5분 | 나가는 패킷 수입니다. 트래픽 선택기 불일치를 야기 하는 터널에서 삭제 합니다. |
|**TunnelIngressBytes** | 바이트 | 5분 | 게이트웨이에서 터널에서 들어오는 트래픽을 합니다.   |
|**TunnelIngressPackets** | 카운트 | 5분 | 게이트웨이에서 터널에서 들어오는 패킷의 수입니다.   |
|**TunnelIngressPacketDropTSMismatch** | 카운트 | 5분 | 트래픽 선택기 불일치를 야기 하는 터널에서 삭제 하는 들어오는 패킷의 수입니다. |


## <a name="setup"></a>메트릭을 기반으로 Azure portal을 사용 하 여 Azure Monitor 경고 설정

다음 예제에서는 단계를 경고에 대 한 게이트웨이 만듭니다.

- **메트릭:** TunnelAverageBandwidth
- **조건:** Bandwidth > 10 bytes/second
- **Window:** 5분
- **경고 작업:** Email



1. 선택한 가상 네트워크 게이트웨이 리소스를 이동할 **경고** 에서 합니다 **모니터링** 탭 합니다. 그런 다음 새 경고 규칙을 만들거나 기존 경고 규칙을 편집 합니다.

   ![경고 규칙을 생성 하기 위한 선택 항목](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "만들기")

2. 리소스와 VPN gateway를 선택 합니다.

   ![단추를 선택 하 고 리소스 목록에서 VPN gateway](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "선택")

3. 경고에 대 한 구성 하는 메트릭을 선택 합니다.

   ![메트릭이 메트릭 목록에서 선택한](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "선택")
4. 신호 논리를 구성 합니다. 세 가지 구성 요소를 가지가 있습니다.

    a. **차원**: 메트릭 차원에 있는 경우 해당 차원의 데이터를 평가 하는 경고는 특정 차원 값을 선택할 수 있습니다. 이 선택적입니다.

    b. **조건**: 메트릭 값을 평가 하려면 작업입니다.

    c. **시간**: 메트릭 데이터의 세분성 및 경고를 평가 하는 기간을 지정 합니다.

   ![세부 정보 구성에 대 한 신호 논리](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "선택")

5. 구성된 된 규칙을 보려면 **경고 규칙 관리**합니다.

   ![경고 규칙을 관리 하기 위한 단추](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "선택")

## <a name="next-steps"></a>다음 단계

터널 진단 로그에 경고를 구성 하려면 [VPN 게이트웨이 진단 로그에 대 한 경고 설정](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)합니다.
