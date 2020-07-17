---
title: 로그 및 메트릭
titleSuffix: Azure Virtual WAN
description: Azure 가상 WAN 로그 및 메트릭에 대 한 자세한 정보
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/05/2020
ms.author: cherylmc
ms.openlocfilehash: 4ce5bc3b4e5ccd26ec3f6b35c995ebe2274042c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84749717"
---
# <a name="azure-virtual-wan-logs-and-metrics"></a>Azure 가상 WAN 로그 및 메트릭

Azure Monitor를 사용 하 여 Azure 가상 WAN을 모니터링할 수 있습니다. 가상 WAN은 여러 네트워킹, 보안 및 라우팅 기능을 결합 하 여 단일 운영 인터페이스를 제공 하는 네트워킹 서비스입니다. 가상 WAN VPN 게이트웨이, Express 경로 게이트웨이 및 Azure 방화벽에는 Azure Monitor를 통해 사용할 수 있는 로깅 및 메트릭이 있습니다. Azure 방화벽 정보는 [Azure 방화벽 로그 및 메트릭](../firewall/logs-and-metrics.md)을 참조 하세요.

이 문서에서는 포털을 통해 사용할 수 있는 메트릭 및 진단에 대해 설명 합니다. 메트릭은 간단한 실시간 시나리오를 지원 하 여 경고 및 빠른 문제 검색에 유용 하 게 사용할 수 있습니다.

## <a name="metrics"></a>메트릭

Azure Monitor 메트릭은 특정 시간에 시스템의 일부 측면을 설명 하는 숫자 값입니다. 메트릭은 1 분 마다 수집 되며 자주 샘플링 될 수 있으므로 경고에 유용 합니다. 비교적 간단한 논리를 사용 하 여 경고를 신속 하 게 발생 시킬 수 있습니다.

### <a name="site-to-site-vpn-gateways"></a>사이트 간 VPN 게이트웨이

다음 메트릭은 Azure 사이트 간 VPN 게이트웨이에 사용할 수 있습니다.

* **게이트웨이 대역폭** – 초당 게이트웨이의 평균 사이트 간 집계 대역폭 (바이트)입니다.
* **터널 대역폭** – 초당 터널의 평균 대역폭 (바이트)입니다.
* **터널 송신 바이트** – 터널의 나가는 바이트 수입니다. 
* **터널 송신 패킷** – 터널의 나가는 패킷 수입니다. 
* 터널 **송신 TS 불일치 패킷 삭제** -터널의 트래픽 선택기 불일치에서 나가는 패킷 삭제 수입니다. 
* **터널 수신 바이트** – 터널의 수신 바이트 수입니다. 
* **터널 수신 패킷** – 터널의 수신 패킷 수입니다. 
* 터널 **수신 TS 불일치 패킷 삭제** -터널의 트래픽 선택기 불일치에서 들어오는 패킷 삭제 수입니다. 

### <a name="point-to-site-vpn-gateways"></a>지점 및 사이트 간 VPN 게이트웨이

다음 메트릭은 Azure 지점 및 사이트 간 VPN 게이트웨이에 사용할 수 있습니다.

* **게이트웨이 P2S 대역폭** – 초당 게이트웨이의 평균 지점 및 사이트 간 집계 대역폭 (바이트)입니다.
* **P2S 연결 수** – 게이트웨이의 지점 및 사이트 간 연결 수입니다.

### <a name="azure-expressroute-gateways"></a>Azure Express 경로 게이트웨이

Azure Express 경로 게이트웨이에서는 다음 메트릭을 사용할 수 있습니다.

* **Bitsinpersecond** – 초당 Azure의 비트입니다.
* **Bitsoutpersecond** – 초당 Azure의 비트 수입니다.

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>게이트웨이 메트릭 보기

다음 단계를 통해 메트릭을 찾고 볼 수 있습니다.

1. 포털에서 게이트웨이가 있는 가상 허브로 이동 합니다.

2. **VPN (사이트 간)** 을 선택 하 여 사이트 간 게이트웨이를 찾습니다. **express** 경로를 선택 하 여 express 경로 게이트웨이를 찾거나 **사용자 VPN (지점 및 사이트 간)** 을 선택 하 여 지점 및 사이트 간 게이트웨이를 찾습니다. 페이지에서 게이트웨이 정보를 볼 수 있습니다. 이 정보를 복사 합니다. 나중에 Azure Monitor를 사용 하 여 진단을 확인 하는 데 사용 합니다.

3. **메트릭**을 선택 합니다.

   :::image type="content" source="./media/logs-metrics/metrics.png" alt-text="성과":::

4. **메트릭** 페이지에서 관심 있는 메트릭을 볼 수 있습니다.

   :::image type="content" source="./media/logs-metrics/metrics-page.png" alt-text="메트릭 페이지":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>진단 로그

### <a name="site-to-site-vpn-gateways"></a>사이트 간 VPN 게이트웨이

다음 진단은 Azure 사이트 간 VPN 게이트웨이에 사용할 수 있습니다.

* **게이트웨이 진단 로그** – 상태, 구성, 서비스 업데이트 및 추가 진단 등의 게이트웨이 관련 진단입니다.
* **터널 진단 로그** – 사이트 간 ipsec 터널, 협상 된 SAs, 연결 끊기 이유 및 추가 진단에 대 한 연결 및 연결 끊기 이벤트와 같은 IPsec 터널 관련 로그입니다.
* **경로 진단 로그** – 정적 경로, BGP, 경로 업데이트 및 추가 진단에 대 한 이벤트와 관련 된 로그입니다.
* **Ike 진단 로그** – IPsec 연결에 대 한 ike 관련 진단입니다.

### <a name="point-to-site-vpn-gateways"></a>지점 및 사이트 간 VPN 게이트웨이

다음 진단은 Azure 지점 및 사이트 간 VPN 게이트웨이에 사용할 수 있습니다.

* **게이트웨이 진단 로그** – 상태, 구성, 서비스 업데이트 및 기타 진단과 같은 게이트웨이 관련 진단입니다.
* **Ike 진단 로그** – IPsec 연결에 대 한 ike 관련 진단입니다.
* **P2S 진단 로그** – 사용자 VPN (지점 및 사이트 간) P2S 구성 및 클라이언트 이벤트입니다. 여기에는 클라이언트 연결/연결 끊기, VPN 클라이언트 주소 할당 및 기타 진단이 포함 됩니다.

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>진단 로그 보기

다음 단계를 통해 진단을 찾고 볼 수 있습니다.

1. 포털에서 가상 WAN 리소스로 이동 합니다. 포털의 가상 WAN 페이지 **개요** 섹션에서 **Essentials** 를 선택 하 여 보기를 확장 하 고 리소스 그룹 정보를 가져옵니다. 리소스 그룹 정보를 복사 합니다.

   :::image type="content" source="./media/logs-metrics/3.png" alt-text="메트릭 페이지":::

2. 모니터링 섹션에서 리소스 그룹으로 이동 합니다. **진단 설정**을 선택 하 고 리소스 정보를 입력 합니다. 이 정보는이 문서 앞부분의 [게이트웨이 메트릭 보기](#metrics-steps) 섹션에서 2 단계에서 복사한 리소스 정보입니다.

   :::image type="content" source="./media/logs-metrics/4.png" alt-text="메트릭 페이지":::

3. 결과 페이지에서 **+ 진단 설정 추가**를 선택한 다음 옵션을 선택 합니다. Log Analytics로 보내거나, 이벤트 허브로 스트림 하거나, 저장소 계정에 보관 하도록 선택할 수 있습니다.

   :::image type="content" source="./media/logs-metrics/5.png" alt-text="메트릭 페이지":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Log Analytics 샘플 쿼리

로그는 **Azure Log Analytics 작업 영역**에 있습니다. Log Analytics에서 쿼리를 설정할 수 있습니다. 다음 예제에는 사이트 간 경로 진단을 가져오는 쿼리가 포함 되어 있습니다.

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

필요에 따라 **= =** 뒤에 아래 값을 바꿉니다.

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog"
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>활동 로그

**활동 로그** 항목은 기본적으로 수집 되며 Azure Portal에서 볼 수 있습니다. Azure 활동 로그 (이전의 *작업 로그* 및 *감사 로그*)를 사용 하 여 azure 구독에 제출 된 모든 작업을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Firewall 로그 및 메트릭을 모니터링하는 방법은 [자습서: Azure Firewall 로그 모니터링](../firewall/tutorial-diagnostics.md)을 참조하세요.
* Azure Monitor의 메트릭에 대 한 자세한 내용은 [Azure Monitor의 메트릭](../azure-monitor/platform/data-platform-metrics.md)을 참조 하세요.
