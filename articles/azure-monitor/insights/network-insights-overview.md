---
title: 네트워크에 대 한 Azure Monitor (미리 보기)
description: 구성 없이 배포 된 모든 네트워크 리소스에 대 한 상태 및 메트릭의 포괄적인 보기를 제공 하는 네트워크 Azure Monitor에 대 한 간략 한 개요입니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/24/2020
ms.openlocfilehash: 0d0e8c4806784cf9b00712ad1bf45bca958a7eb1
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91995361"
---
# <a name="azure-monitor-for-networks-preview"></a>네트워크에 대 한 Azure Monitor (미리 보기)
네트워크에 대 한 Azure Monitor는 구성 없이 배포 된 모든 네트워크 리소스에 대 한 [상태](../../service-health/resource-health-checks-resource-types.md) 및 [메트릭의](../platform/metrics-supported.md) 포괄적인 보기를 제공 합니다.  또한 [연결 모니터](../../network-watcher/connection-monitor-preview.md), [nsgs (네트워크 보안 그룹)에 대 한 흐름 로깅](../../network-watcher/network-watcher-nsg-flow-logging-overview.md), [트래픽 분석](../../network-watcher/traffic-analytics.md)및 기타 네트워크 [진단](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) 기능과 같은 모든 네트워크 모니터링 기능에 대 한 액세스를 제공 합니다.

네트워크에 대 한 Azure Monitor는 다음과 같은 모니터링의 주요 구성 요소를 중심으로 구성 됩니다.
- [네트워크 상태 및 메트릭](#networkhealth)
- [연결](#connectivity)
- [트래픽](#traffic)
- [진단 도구 키트](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>네트워크 상태 및 메트릭

네트워크 **개요** 페이지의 Azure Monitor는 리소스 상태 및 경고와 함께 네트워킹 리소스의 인벤토리를 시각화 하는 간편한 방법을 제공 합니다. 검색 및 필터링, Resource Health 및 메트릭, 경고의 네 가지 주요 기능 영역으로 구분 됩니다. 및 종속성 보기

![개요 페이지](media/network-insights-overview/overview.png)

### <a name="search-and-filtering"></a>검색 및 필터링
**구독**, **리소스 그룹** 및 **리소스 종류**와 같은 필터를 사용 하 여 리소스 상태 및 경고 보기를 사용자 지정할 수 있습니다. 검색 상자는 리소스 속성을 검색 하는 기능을 제공 합니다.

검색 상자를 사용 하 여 리소스 및 연결 된 리소스를 검색할 수 있습니다. 예를 들어 공용 IP는 Application Gateway 연결 됩니다. 공용 IP DNS 이름 검색은 공용 IP와 연결 된 Application Gateway를 모두 식별 합니다.

![네트워크에 대 한 Azure Monitor-검색](media/network-insights-overview/search.png)


### <a name="resource-health-and-metric"></a>Resource Health 및 메트릭
각 타일은 리소스 상태와 함께 선택한 모든 구독에 배포 되는 인스턴스 수를 포함 하는 리소스 종류를 나타냅니다. 아래 예제에서는 45 ER 및 VPN 연결을 배포 하 고, 44은 정상 이며, 1은 사용할 수 없습니다.

![네트워크에 대 한 Azure Monitor-리소스 상태](media/network-insights-overview/resource-health.png)

사용할 수 없는 ER 및 VPN 연결을 클릭 하면 메트릭 보기가 시작 됩니다. 

![네트워크 메트릭 보기에 대 한 Azure Monitor](media/network-insights-overview/metric-view.png)

표 뷰에서 각 요소를 클릭할 수 있습니다. 상태 아이콘을 클릭 하 여 해당 연결에 대 한 리소스 상태로 리디렉션합니다. 경고를 클릭 하 여 해당 연결에 대 한 경고 및 메트릭 페이지로 각각 리디렉션합니다. 

### <a name="alerts"></a>경고
오른쪽의 **경고** 표에서는 모든 구독에서 선택한 리소스에 대해 생성 된 모든 경고의 보기를 제공 합니다. 경고 횟수를 클릭 하 여 자세한 경고 페이지로 이동 합니다.

### <a name="dependency-view"></a>종속성 보기
**종속성** 뷰를 사용 하 여 리소스를 구성 하는 방법을 시각화할 수 있습니다. 현재 종속성 보기는 Application Gateway, 가상 WAN 및 Load Balancer에 대해 지원 됩니다. 예를 들어 Application Gateway의 경우 메트릭 그리드 보기에서 Application Gateway 리소스 이름을 클릭 하 여 종속성 보기에 액세스할 수 있습니다. 이는 가상 WAN 및 Load Balancer에도 적용 됩니다.

![네트워크에 대 한 Azure Monitor-Application Gateway 보기](media/network-insights-overview/application-gateway.png)

Application Gateway에 대 한 **종속성** 보기는 프런트 엔드 ip를 수신기, 규칙 및 백 엔드 풀에 연결 하는 방법을 간략하게 보여 줍니다. 연결 가장자리는 색으로 구분 되며 백 엔드 풀 상태에 따라 추가 세부 정보를 제공 합니다. 이 보기에는 가상 머신 확장 집합 및 VM 인스턴스와 같은 관련 된 모든 백 엔드 풀에 대 한 Application Gateway 메트릭과 메트릭의 상세 보기가 제공 됩니다.

![네트워크에 대 한 Azure Monitor-종속성 보기](media/network-insights-overview/dependency-view.png)

종속성 그래프를 사용 하면 구성 설정을 쉽게 탐색할 수 있습니다. 백 엔드 풀을 마우스 오른쪽 단추로 클릭 하 여 다른 기능에 액세스 합니다. 예를 들어 백 엔드 풀이 VM 인 경우 VM Insights에 직접 액세스 하 고 연결 문제를 확인 하는 Network Watcher 연결 문제를 해결할 수 있습니다.

![네트워크에 대 한 Azure Monitor-종속성 보기 메뉴](media/network-insights-overview/dependency-view-menu.png)

종속성 보기의 검색 및 필터 모음은 그래프를 검색 하는 간편한 방법을 제공 합니다. 예를 들어 아래 예제에서 *AppGWTestRule* 를 검색 하면 *AppGWTestRule*를 통해 연결 된 모든 노드로 그래픽 보기의 범위를 좁힐 수 있습니다.

![네트워크용 Azure Monitor-검색 예제](media/network-insights-overview/search-example.png)

다른 필터를 통해 특정 경로 및 상태에 대 한 범위를 좁힐 수 있습니다. 예를 들어 상태가 *비정상*인 모든 가장자리를 표시 하려면 상태 드롭다운에서 *비정상* **상태를** 선택 합니다.

**자세한 메트릭 보기** 를 클릭 하 여 Application Gateway, 모든 백 엔드 풀 리소스 및 프런트 엔드 ip에 대 한 자세한 메트릭이 포함 된 미리 구성 된 통합 문서를 시작 합니다. 

## <a name="connectivity"></a><a name="connectivity"></a>연결

연결 **탭은** 선택한 구독 집합에 대해 연결 모니터 및 [연결 모니터 (미리 보기)](../../network-watcher/connection-monitor-preview.md) 를 사용 하 여 구성 된 모든 테스트를 시각화 하는 간편한 방법을 제공 합니다.

![네트워크에 대 한 Azure Monitor의 연결 탭](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

테스트는 원본 및 대상 타일로 그룹화 되며 각 테스트에 대 한 연결 가능성 상태를 표시 합니다. 연결할 수 있는 설정에 따라 실패 한 검사 (%)에 따라 연결 가능성 조건을 구성할 수 있습니다. 및 RTT (밀리초)입니다. 값이 설정 되 면 선택 조건에 따라 각 테스트의 상태가 업데이트 됩니다.

![네트워크에 대 한 Azure Monitor의 연결 테스트](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)

원본 또는 대상 타일을 클릭 하면 메트릭 보기가 시작 됩니다.

![네트워크에 대 한 Azure Monitor의 연결 메트릭](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)


표 뷰에서 각 요소를 클릭할 수 있습니다. 연결 아이콘을 클릭 하 여 **연결 모니터** 포털 페이지로 리디렉션하여 홉 토폴로지와 연결 된 **문제에 영향** 을 미치는 홉 수를 확인 합니다. **경고를 클릭 하 여** 경고로 리디렉션하고 **실패 한 백분율/왕복 시간을 확인** 하 여 선택한 연결 모니터에 대 한 메트릭 페이지로 리디렉션합니다.

오른쪽의 **경고**   표는 모든 구독에서 구성 된 연결 테스트에 대해 생성 된 모든 경고의 보기를 제공 합니다. 경고 횟수를 클릭 하 여 자세한 경고 페이지로 이동 합니다.

## <a name="traffic"></a><a name="traffic"></a>트래픽
트래픽 탭에서는 [Nsgs 흐름 로그](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) 에 대해 구성 된 모든 nsgs에 대 한 액세스를 제공 하 고, 선택한 구독 집합에 대해 [트래픽 분석](../../network-watcher/traffic-analytics.md) 하 고, 위치 별로 그룹화 할 수 있습니다. 이 탭에서 제공 하는 검색 기능을 사용 하면 검색 된 IP 주소에 대해 구성 된 NSGs를 식별할 수 있습니다. 사용자 환경에서 모든 IP 주소를 검색할 수 있으며, 바둑판식으로 표시 된 지역 보기는 NSGS 흐름 로그 및 트래픽 분석 구성 상태와 함께 모든 NSGs를 표시 합니다.

![네트워크에 대 한 Azure Monitor의 트래픽 보기](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)

모든 지역 타일을 클릭 하면 NSG 흐름 로그 및 트래픽 분석을 쉽게 보고 구성할 수 있는 그리드 보기가 시작 됩니다.  

![네트워크에 대 한 Azure Monitor의 트래픽 영역 보기](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)

표 뷰에서 각 요소를 클릭할 수 있습니다. 구성 상태를 클릭 하 여 NSG 흐름 로그를 편집 하 고 구성을 트래픽 분석 합니다. 경고를 클릭 하 여 선택 된 NSG에 대해 구성 된 트래픽 경고로 리디렉션합니다. 마찬가지로 작업 영역을 클릭 하 여 트래픽 분석 보기로 이동할 수 있습니다.  

오른쪽의 **경고**   표는 모든 구독에서 모든 트래픽 분석 작업 영역 기반 경고에 대 한 보기를 제공 합니다. 경고 횟수를 클릭 하 여 자세한 경고 페이지로 이동 합니다.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> 진단 도구 키트
진단 도구 키트는 네트워크 문제 해결에 사용할 수 있는 모든 진단 기능에 대 한 액세스를 제공 합니다. 이 드롭다운에서 [패킷 캡처](../../network-watcher/network-watcher-packet-capture-overview.md), [VPN 문제 해결](../../network-watcher/network-watcher-troubleshoot-overview.md), [연결 문제 해결](../../network-watcher/network-watcher-connectivity-overview.md), [다음 홉](../../network-watcher/network-watcher-next-hop-overview.md) , [IP 흐름 확인](../../network-watcher/network-watcher-ip-flow-verify-overview.md)등의 기능에 액세스할 수 있습니다.

![진단 도구 키트 탭](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>문제 해결 

일반적인 문제 해결 지침은 전용 통합 문서 기반 insights [문제 해결 문서](troubleshoot-workbooks.md)를 참조 하세요.

이 섹션에서는 네트워크에 대 한 Azure Monitor를 사용 하는 경우 발생할 수 있는 몇 가지 일반적인 문제를 진단 하 고 해결 하는 데 도움이 됩니다. 아래 목록에서 특정 문제와 관련된 정보를 찾을 수 있습니다.

### <a name="resolving-performance-issues-or-failures"></a>성능 문제 또는 오류 해결

네트워크 Azure Monitor에서 식별 하는 네트워킹 관련 문제를 해결 하려면 오작동 하는 리소스의 문제 해결 설명서를 참조 하세요. 자주 사용 하는 서비스에 대 한 문제 해결 링크는 아래에 나열 되어 있습니다.
* VNET (Virtual Network)
* Application Gateway
* VPN Gateway
* ExpressRoute 
* Load Balancer 

### <a name="why-dont-i-see-the-resources-from-all-the-subscriptions-i-have-selected"></a>선택한 모든 구독의 리소스가 표시 되지 않는 이유는 무엇 인가요?

Network Insights는 한 번에 5 개 구독의 리소스만 표시할 수 있습니다. 

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-network-insights-how-do-i-do-so"></a>네트워크 정보를 변경 하거나 추가 하려면 어떻게 해야 할까요?

변경하려면 "편집 모드"를 선택하여 통합 문서를 수정합니다. 그런 다음, 지정된 구독 및 리소스 그룹에 연결된 새 통합 문서로 작업을 저장할 수 있습니다.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>통합 문서의 모든 부분을 고정 한 후의 시간-그레인

"자동" 시간 범위를 활용하므로 선택한 시간 범위에 따라 달라집니다.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>통합 문서 일부가 고정 된 시간 범위

시간 범위는 대시보드 설정에 따라 달라집니다.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-network-insights"></a>다른 데이터를 보거나 나만의 시각화를 만들려면 어떻게 해야 하나요? 네트워크 정보를 변경 하려면 어떻게 해야 하나요?

편집 모드를 사용 하 여 모든 측면 패널 및 상세 메트릭 보기에서 볼 수 있는 통합 문서를 편집한 다음 새 변경 내용을 모두 포함 하는 새 통합 문서로 작업을 저장할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- [Azure Network Watcher의](../../network-watcher/network-watcher-monitoring-overview.md)네트워크 모니터링에 대해 자세히 알아보세요.
- [Azure Monitor 통합 문서를 사용하여 대화형 보고서 만들기](../platform/workbooks-overview.md)를 검토하여 통합 문서에서 지원하도록 디자인된 시나리오, 새 보고서를 작성하고 기존 보고서를 사용자 지정하는 방법 등을 알아보세요.