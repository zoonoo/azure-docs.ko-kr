---
title: 네트워크 미리 보기 Azure Monitor
description: 구성 없이 배포 된 모든 네트워크 리소스에 대 한 상태 및 메트릭의 포괄적인 보기를 제공 하는 네트워크의 Azure Monitor에 대 한 개요입니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/24/2020
ms.openlocfilehash: e2a43c4d0423b286984631fda75e5ff806ae9a57
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102764"
---
# <a name="azure-monitor-for-networks-preview"></a>네트워크 미리 보기 Azure Monitor
네트워크에 대 한 Azure Monitor는 구성 없이 모든 배포 된 네트워크 리소스에 대 한 [상태](../../service-health/resource-health-checks-resource-types.md) 및 [메트릭의](../platform/metrics-supported.md) 포괄적인 보기를 제공 합니다. 또한 [연결 모니터](../../network-watcher/connection-monitor-preview.md), [nsgs (네트워크 보안 그룹)에 대 한 흐름 로깅](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)및 [트래픽 분석](../../network-watcher/traffic-analytics.md)같은 네트워크 모니터링 기능에 대 한 액세스를 제공 합니다. 그리고 다른 네트워크 [진단](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) 기능을 제공 합니다.

네트워크에 대 한 Azure Monitor는 모니터링의 주요 구성 요소를 기준으로 구성 됩니다.
- [네트워크 상태 및 메트릭](#networkhealth)
- [연결](#connectivity)
- [트래픽](#traffic)
- [진단 도구 키트](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>네트워크 상태 및 메트릭

네트워크 **개요** 페이지의 Azure Monitor는 네트워킹 리소스 인벤토리를 리소스 상태 및 경고와 함께 쉽게 시각화할 수 있는 방법을 제공 합니다. 검색 및 필터링, 리소스 상태, 메트릭, 경고 및 종속성 보기의 네 가지 주요 기능 영역으로 구분 됩니다.

![개요 페이지를 보여 주는 스크린샷](media/network-insights-overview/overview.png)

### <a name="search-and-filtering"></a>검색 및 필터링
**구독**, **리소스 그룹**및 **유형과**같은 필터를 사용 하 여 리소스 상태 및 경고 보기를 사용자 지정할 수 있습니다.

검색 상자를 사용 하 여 리소스 및 관련 리소스를 검색할 수 있습니다. 예를 들어 공용 IP는 응용 프로그램 게이트웨이와 연결 됩니다. 공용 ip의 DNS 이름 검색은 공용 IP와 연결 된 application gateway를 모두 반환 합니다.

![네트워크 검색 결과에 대 한 Azure Monitor를 보여 주는 스크린샷](media/network-insights-overview/search.png)


### <a name="resource-health-and-metrics"></a>리소스 상태 및 메트릭
다음 예제에서 각 타일은 리소스 유형을 나타냅니다. 타일에는 선택한 모든 구독에서 배포 된 해당 리소스 유형의 인스턴스 수가 표시 됩니다. 또한 리소스의 상태를 표시 합니다. 이 예제에서는 105 ER 및 VPN 연결을 배포 합니다. 103는 정상 이며 2는 사용할 수 없습니다.

![네트워크에 대 한 Azure Monitor의 리소스 상태 및 메트릭을 보여 주는 스크린샷](media/network-insights-overview/resource-health.png)

사용할 수 없는 ER 및 VPN 연결을 선택 하면 메트릭 보기가 표시 됩니다. 

![네트워크에 대 한 Azure Monitor의 메트릭 보기를 보여 주는 스크린샷](media/network-insights-overview/metric-view.png)

표 뷰에서 모든 항목을 선택할 수 있습니다. **상태** 열에서 아이콘을 선택 하 여 해당 연결에 대 한 리소스 상태를 가져옵니다. **경고** 열에서 값을 선택 하 여 연결에 대 한 경고 및 메트릭 페이지로 이동 합니다. 

### <a name="alerts"></a>경고
페이지의 오른쪽에 있는 **경고** 상자에는 모든 구독에서 선택한 리소스에 대해 생성 된 모든 경고의 보기가 제공 됩니다. 경고 횟수를 선택 하 여 자세한 경고 페이지로 이동 합니다.

### <a name="dependency-view"></a>종속성 보기
종속성 뷰를 사용 하면 리소스가 구성 되는 방식을 시각화할 수 있습니다. 종속성 보기는 현재 Azure 애플리케이션 게이트웨이, Azure 가상 WAN 및 Azure Load Balancer에 대해 사용할 수 있습니다. 예를 들어 Application Gateway의 경우 메트릭 그리드 보기에서 Application Gateway 리소스 이름을 선택 하 여 종속성 보기에 액세스할 수 있습니다. 가상 WAN 및 Load Balancer에 대해 동일한 작업을 수행할 수 있습니다.

![네트워크에 대 한 Azure Monitor의 Application Gateway 보기를 보여 주는 sreenshot](media/network-insights-overview/application-gateway.png)

Application Gateway에 대 한 종속성 보기는 프런트 엔드 Ip를 수신기, 규칙 및 백 엔드 풀에 연결 하는 방법에 대 한 간단한 보기를 제공 합니다. 연결선은 색으로 구분 되며 백 엔드 풀 상태에 따라 추가 세부 정보를 제공 합니다. 이 보기에는 가상 머신 확장 집합 및 VM 인스턴스와 같은 관련 된 모든 백 엔드 풀에 대 한 Application Gateway 메트릭 및 메트릭에 대 한 자세한 보기가 제공 됩니다.

![네트워크에 대 한 Azure Monitor에서 종속성 보기를 보여 주는 스크린샷](media/network-insights-overview/dependency-view.png)

종속성 그래프는 구성 설정에 대 한 간편한 탐색을 제공 합니다. 백 엔드 풀을 마우스 오른쪽 단추로 클릭 하 여 다른 정보에 액세스 합니다. 예를 들어 백 엔드 풀이 VM 인 경우 VM 정보 및 Azure Network Watcher 연결 문제 해결을 직접 액세스 하 여 연결 문제를 식별할 수 있습니다.

![네트워크에 대 한 Azure Monitor 종속성 보기 메뉴를 보여 주는 스크린샷](media/network-insights-overview/dependency-view-menu.png)

종속성 보기의 검색 및 필터 모음은 그래프를 검색 하는 쉬운 방법을 제공 합니다. 예를 들어 이전 예제에서 **AppGWTestRule** 를 검색 하는 경우 뷰는 AppGWTestRule를 통해 연결 된 모든 노드로 축소 됩니다.

![네트워크에 대 한 Azure Monitor 검색의 예를 보여 주는 스크린샷](media/network-insights-overview/search-example.png)

다양 한 필터를 통해 특정 경로 및 상태로 확장할 수 있습니다. 예를 들어 상태가 비정상 인 모든 가장자리를 표시 하려면 **상태 목록에서** **비정상** 상태만을 선택 합니다.

Application gateway, 모든 백 엔드 풀 리소스, 프런트 엔드 Ip에 대 한 상세 메트릭을 제공 하는 미리 구성 된 통합 문서를 열려면 **자세한 메트릭 보기** 를 선택 합니다. 

## <a name="connectivity"></a><a name="connectivity"></a>연결

연결 **탭은** 선택한 구독 집합에 대해 연결 모니터 및 [연결 모니터 (미리 보기)](../../network-watcher/connection-monitor-preview.md) 를 통해 구성 된 모든 테스트를 시각화 하는 쉬운 방법을 제공 합니다.

![네트워크에 대 한 Azure Monitor의 연결 탭을 보여 주는 스크린샷](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

테스트는 **원본** 및 **대상** 타일로 그룹화 되며 각 테스트에 대 한 연결 가능성 상태를 표시 합니다. 연결할 수 있는 설정은 실패 한 검사 (%)에 따라 연결 조건에 대 한 구성에 쉽게 액세스할 수 있도록 합니다. 및 RTT (밀리초)입니다. 값을 설정한 후에는 선택 조건에 따라 각 테스트의 상태를 업데이트 합니다.

![네트워크에 대 한 Azure Monitor의 연결 테스트를 보여 주는 스크린샷](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)

모든 원본 또는 대상 타일을 선택 하 여 메트릭 보기를 열 수 있습니다.

![네트워크에 대 한 Azure Monitor의 연결 메트릭을 보여 주는 스크린샷](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)


표 뷰에서 모든 항목을 선택할 수 있습니다. 연결 모니터 포털 페이지로 이동 하려면 연결 **의 열에서** 아이콘을 선택 하 고 식별 된 문제에 영향을 주는 홉 단위 토폴로지 및 연결을 확인 합니다. **경고 열에서** 값을 선택 하 여 경고로 이동 합니다. **실패 한 비율 검사** 및 **왕복 시간 (ms)** 열에서 그래프를 선택 하 여 선택한 연결 모니터에 대 한 메트릭 페이지로 이동 합니다.

페이지의 오른쪽에 있는 **경고** 상자는 모든 구독에서 구성 된 연결 테스트에 대해 생성 된 모든 경고의 보기를 제공 합니다. 경고 횟수를 선택 하 여 자세한 경고 페이지로 이동 합니다.

## <a name="traffic"></a><a name="traffic"></a>트래픽
**트래픽** 탭에서는 [nsgs 흐름 로그](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) 에 대해 구성 된 모든 nsgs에 대 한 액세스를 제공 하 고, 선택한 구독 집합에 대 한 [트래픽 분석](../../network-watcher/traffic-analytics.md) 를 위치별 그룹화 하 여 제공 합니다. 이 탭에서 제공 하는 검색 기능을 사용 하면 검색 된 IP 주소에 대해 구성 된 NSGs를 식별할 수 있습니다. 사용자 환경에서 모든 IP 주소를 검색할 수 있습니다. 바둑판식으로 표시 된 지역 보기에는 NSGS 흐름 로그 및 트래픽 분석 구성 상태와 함께 모든 NSGs가 표시 됩니다.

![네트워크에 대 한 Azure Monitor의 트래픽 탭을 보여 주는 스크린샷](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)

영역 타일을 선택 하면 그리드 뷰가 나타납니다. 표는 NSG 흐름 로그를 제공 하 고 쉽게 읽고 구성할 수 있는 보기에서 트래픽 분석 합니다.  

![네트워크에 대 한 Azure Monitor의 트래픽 영역 보기를 보여 주는 스크린샷](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)

표 뷰에서 모든 항목을 선택할 수 있습니다. **Flowlog 구성 상태** 열에서 아이콘을 선택 하 여 nsg 흐름 로그 및 트래픽 분석 구성을 편집 합니다. **경고** 열에서 값을 선택 하 여 선택 된 nsg에 대해 구성 된 트래픽 경고로 이동 합니다. 마찬가지로 **트래픽 분석 작업 영역**을 선택 하 여 트래픽 분석 보기로 이동할 수 있습니다.  

페이지의 오른쪽에 있는 **경고** 상자는 모든 구독에서 모든 트래픽 분석 작업 영역 기반 경고에 대 한 보기를 제공 합니다. 경고 횟수를 선택 하 여 자세한 경고 페이지로 이동 합니다.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> 진단 도구 키트
진단 도구 키트는 네트워크 문제 해결에 사용할 수 있는 모든 진단 기능에 대 한 액세스를 제공 합니다. 이 드롭다운 목록을 사용 하 여 [패킷 캡처](../../network-watcher/network-watcher-packet-capture-overview.md), [VPN 문제 해결](../../network-watcher/network-watcher-troubleshoot-overview.md), [연결 문제 해결](../../network-watcher/network-watcher-connectivity-overview.md), [다음 홉](../../network-watcher/network-watcher-next-hop-overview.md), [IP 흐름 확인](../../network-watcher/network-watcher-ip-flow-verify-overview.md)등의 기능에 액세스할 수 있습니다.

![진단 도구 키트 탭을 보여 주는 스크린샷](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>문제 해결 

일반적인 문제 해결 지침은 전용 통합 문서 기반 insights [문제 해결 문서](troubleshoot-workbooks.md)를 참조 하세요.

이 섹션에서는 네트워크에 대 한 Azure Monitor를 사용 하는 경우 발생할 수 있는 몇 가지 일반적인 문제를 진단 하 고 해결 하는 데 도움이 됩니다. 

### <a name="how-do-i-resolve-performance-problems-or-failures"></a>성능 문제 또는 오류를 해결할 어떻게 할까요? 있습니까?

네트워크 Azure Monitor에서 식별 하는 네트워킹 관련 문제를 해결 하는 방법에 대 한 자세한 내용은 작동 하지 않는 리소스에 대 한 문제 해결 설명서를 참조 하세요. 

자주 사용 하는 서비스에 대 한 문제 해결 문서에 대 한 몇 가지 링크는 다음과 같습니다. 이러한 서비스에 대 한 자세한 문제 해결 문서는 해당 서비스에 대 한 목차의 문제 해결 섹션에 있는 다른 문서를 참조 하세요.
* [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-peering-issues)
* [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-gateway-internal-load-balancer-app-service-environment)
* [Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot)
* [Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-expressroute-overview) 
* [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-troubleshoot) 

### <a name="why-dont-i-see-the-resources-for-all-the-subscriptions-ive-selected"></a>선택한 모든 구독에 대 한 리소스가 표시 되지 않는 이유는 무엇 인가요?

Network Insights는 한 번에 5 개의 구독에 대해서만 리소스를 표시할 수 있습니다. 

### <a name="how-do-i-make-changes-or-add-visualizations-to-network-insights"></a>변경 하거나 시각화를 Network Insights에 추가 어떻게 할까요??

변경을 수행 하려면 **편집 모드** 를 선택 하 여 통합 문서를 수정 합니다. 그런 다음 지정 된 구독 및 리소스 그룹에 연결 된 새 통합 문서로 변경 내용을 저장할 수 있습니다.

### <a name="whats-the-time-grain-after-i-pin-any-part-of-the-workbooks"></a>통합 문서의 일부를 고정 한 후의 시간 수준은 무엇 인가요?

Network Insights는 **자동** 시간 수준을 사용 하므로 시간 수준은 선택한 시간 범위를 기준으로 합니다.

### <a name="whats-the-time-range-when-any-part-of-a-workbook-is-pinned"></a>통합 문서 일부가 고정 된 시간 범위는 어떻게 되나요?

시간 범위는 대시보드 설정에 따라 달라 집니다.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-network-insights"></a>다른 데이터를 보거나 나만의 시각화를 만들려면 어떻게 해야 하나요? 네트워크 정보를 어떻게 변경할 수 있나요?

편집 모드를 사용 하 여 모든 측면 패널 또는 상세 메트릭 보기에서 볼 수 있는 통합 문서를 편집할 수 있습니다. 그런 다음 변경 내용을 새 통합 문서로 저장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 네트워크 모니터링에 대해 자세히 알아보기: [Azure Network Watcher 이란?](../../network-watcher/network-watcher-monitoring-overview.md)
- 통합 문서에서 지원 하도록 디자인 된 시나리오, 보고서를 만들고 기존 보고서를 사용자 지정 하는 방법 및 기타: [Azure Monitor 통합 문서를 사용 하 여 대화형 보고서 만들기](../platform/workbooks-overview.md)
