---
title: 네트워크에 대 한 Azure 모니터 (미리 보기)
description: 구성 없이 배포된 모든 네트워크 리소스에 대한 상태 및 메트릭에 대한 포괄적인 보기를 제공하는 네트워크에 대한 Azure Monitorfor에 대한 빠른 개요입니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 0f5b2fbd13cb9658e255fde727e115df748aaed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654871"
---
# <a name="azure-monitor-for-networks-preview"></a>네트워크에 대 한 Azure 모니터 (미리 보기)
네트워크에 대한 Azure Monitor는 구성 없이 배포된 모든 네트워크 리소스에 대한 상태 및 메트릭에 대한 포괄적인 보기를 제공합니다. 고급 검색 기능을 사용하면 리소스 종속성을 식별할 수 있으므로 호스팅되는 웹 사이트 이름을 검색하기만 하면 웹 사이트를 호스팅하는 리소스를 식별하는 등의 시나리오가 가능합니다.

네트워크용 Azure 모니터 **개요** 페이지는 리소스 상태 및 경고와 함께 네트워킹 리소스의 인벤토리를 쉽게 시각화할 수 있는 방법을 제공합니다. 그것은 네 가지 주요 기능 영역으로 나뉩니다 :

- 검색 및 필터링
- 리소스 상태 및 메트릭
- 경고 
- 종속성 보기

![개요 페이지](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>검색 및 필터링
리소스 상태 및 경고 보기는 **구독,** 리소스 **그룹** 및 **리소스 유형**과 같은 필터를 사용하여 사용자 지정할 수 있습니다. 검색 상자는 리소스 속성을 검색하는 기능을 제공합니다.

검색 상자를 사용하여 리소스 및 관련 리소스를 검색할 수 있습니다. 예를 들어 공용 IP는 응용 프로그램 게이트웨이에 연결됩니다. 공용 IP DNS 이름을 검색하면 공용 IP와 연결된 응용 프로그램 게이트웨이를 모두 식별합니다.

![검색](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>리소스 상태 및 메트릭
각 타일은 리소스 상태와 함께 선택한 모든 구독에 배포된 인스턴스 수를 나타내는 리소스 유형을 나타냅니다. 아래 예제에서는 105개의 ER 및 VPN 연결이 배포되고 103개는 정상이고 2개는 사용할 수 없습니다.

![리소스 상태](media/network-insights-overview/resource-health.png)

사용할 수 없는 두 개의 ER 및 VPN 연결을 클릭하면 메트릭 보기가 시작됩니다. 

![메트릭 보기](media/network-insights-overview/metric-view.png)

그리드 보기에서 각 요소를 클릭할 수 있습니다. 해당 연결의 리소스 상태로 리디렉션하려면 상태 아이콘을 클릭합니다. 경고를 클릭하여 해당 연결에 대한 경고 및 메트릭 페이지로 리디렉션합니다. 

## <a name="alerts"></a>경고
오른쪽의 **경고** 표는 모든 구독에서 선택한 리소스에 대해 생성된 모든 경고를 볼 수 있습니다. 경고 수를 클릭하여 자세한 경고 페이지로 이동합니다.

## <a name="dependency-view"></a>종속성 보기
**종속성** 보기는 리소스가 구성된 방법을 시각화하는 데 도움이 됩니다. 현재 종속성 보기는 응용 프로그램 게이트웨이에 대해서만 지원됩니다. 메트릭 그리드 보기에서 응용 프로그램 게이트웨이 리소스 이름을 클릭하여 종속성 보기에 액세스할 수 있습니다.

![응용 프로그램 게이트웨이 보기](media/network-insights-overview/application-gateway.png)

응용 프로그램 게이트웨이에 대한 **종속성** 보기는 프런트 엔드 IP가 수신기, 규칙 및 백 엔드 풀에 연결되는 방식에 대한 단순화된 보기를 제공합니다. 연결 모서리는 색상으로 구분되며 백 엔드 풀 상태에 따라 추가 세부 정보를 제공합니다. 또한 이 보기는 VMSS 및 VM 인스턴스와 같은 모든 관련 백 엔드 풀에 대한 애플리케이션 게이트웨이 메트릭 및 메트릭에 대한 자세한 보기를 제공합니다.

![종속성 보기](media/network-insights-overview/dependency-view.png)

종속성 그래프를 사용하면 구성 설정으로 쉽게 탐색할 수 있습니다. 백 엔드 풀을 마우스 오른쪽 버튼으로 클릭하여 다른 기능에 액세스할 수 있습니다. 예를 들어 백 엔드 풀이 VM인 경우 VM Insights 및 네트워크 감시자 연결 문제 해결에 직접 액세스하여 연결 문제를 식별할 수 있습니다.

![종속성 보기 메뉴](media/network-insights-overview/dependency-view-menu.png)

종속성 보기의 검색 및 필터 막대는 그래프를 통해 쉽게 검색할 수 있는 방법을 제공합니다. 예를 들어 아래 예제에서 *AppGWTestRule을* 검색하면 *AppGWTestRule*을 통해 연결된 모든 노드로 그래픽 보기가 좁아집니다. 

![검색 예제](media/network-insights-overview/search-example.png)

필터가 다르면 특정 경로와 상태로 좁히는 데 도움이 됩니다. 예를 들어 **상태 상태** 드롭다운에서 *비정상만* 선택하여 상태가 *비정상인*모든 가장자리를 표시합니다.

자세한 **메트릭 보기를** 클릭하여 응용 프로그램 게이트웨이, 모든 백 엔드 풀 리소스 및 프런트 엔드 IP에 대한 자세한 메트릭이 있는 미리 구성된 통합 문서를 시작합니다. 

## <a name="next-steps"></a>다음 단계 

- 네트워크 모니터링에 대해 자세히 알아보려면 [Azure 네트워크 감시자란 무엇입니까?](/azure/network-watcher/network-watcher-monitoring-overview)
