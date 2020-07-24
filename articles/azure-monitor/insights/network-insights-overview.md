---
title: 네트워크에 대 한 Azure Monitor (미리 보기)
description: 구성 없이 배포 된 모든 네트워크 리소스에 대 한 상태 및 메트릭의 포괄적인 보기를 제공 하는 네트워크 Azure Monitor에 대 한 간략 한 개요입니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: ccc5578944dc6eea9a62360045272896d78e2fac
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87128678"
---
# <a name="azure-monitor-for-networks-preview"></a>네트워크에 대 한 Azure Monitor (미리 보기)
네트워크에 대 한 Azure Monitor는 구성 없이 배포 된 모든 네트워크 리소스에 대 한 상태 및 메트릭의 포괄적인 보기를 제공 합니다. 고급 검색 기능을 사용 하면 호스트 된 웹 사이트 이름을 간단히 검색 하 여 웹 사이트를 호스트 하는 리소스를 식별 하는 등의 시나리오를 통해 리소스 종속성을 식별할 수 있습니다.

네트워크 **개요** 페이지의 Azure Monitor는 리소스 상태 및 경고와 함께 네트워킹 리소스의 인벤토리를 시각화 하는 간편한 방법을 제공 합니다. 이는 다음과 같은 네 가지 주요 기능 영역으로 구분 됩니다.

- 검색 및 필터링
- Resource Health 및 메트릭
- 경고 
- 종속성 보기

![개요 페이지](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>검색 및 필터링
**구독**, **리소스 그룹** 및 **리소스 종류**와 같은 필터를 사용 하 여 리소스 상태 및 경고 보기를 사용자 지정할 수 있습니다. 검색 상자는 리소스 속성을 검색 하는 기능을 제공 합니다.

검색 상자를 사용 하 여 리소스 및 연결 된 리소스를 검색할 수 있습니다. 예를 들어 공용 IP는 Application Gateway 연결 됩니다. 공용 IP DNS 이름 검색은 공용 IP와 연결 된 Application Gateway를 모두 식별 합니다.

![검색](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Resource Health 및 메트릭
각 타일은 리소스 상태와 함께 선택한 모든 구독에 배포 되는 인스턴스 수를 포함 하는 리소스 종류를 나타냅니다. 아래 예제에서는 105 ER 및 VPN 연결을 배포 하 고 103가 정상 이며 2를 사용할 수 없습니다.

![리소스 상태](media/network-insights-overview/resource-health.png)

사용할 수 없는 두 개의 ER 및 VPN 연결을 클릭 하면 메트릭 보기가 시작 됩니다. 

![메트릭 보기](media/network-insights-overview/metric-view.png)

표 뷰에서 각 요소를 클릭할 수 있습니다. 상태 아이콘을 클릭 하 여 해당 연결에 대 한 리소스 상태로 리디렉션합니다. 경고를 클릭 하 여 해당 연결에 대 한 경고 및 메트릭 페이지로 각각 리디렉션합니다. 

## <a name="alerts"></a>경고
오른쪽의 **경고** 표에서는 모든 구독에서 선택한 리소스에 대해 생성 된 모든 경고의 보기를 제공 합니다. 경고 횟수를 클릭 하 여 자세한 경고 페이지로 이동 합니다.

## <a name="dependency-view"></a>종속성 보기
**종속성** 뷰를 사용 하 여 리소스를 구성 하는 방법을 시각화할 수 있습니다. 현재 종속성 보기는 Application Gateway, 가상 WAN 및 Load Balancer에 대해 지원 됩니다. 예를 들어 Application Gateway의 경우 메트릭 그리드 보기에서 Application Gateway 리소스 이름을 클릭 하 여 종속성 보기에 액세스할 수 있습니다. 이는 가상 WAN 및 Load Balancer에도 적용 됩니다. 

![Application Gateway 뷰](media/network-insights-overview/application-gateway.png)

Application Gateway에 대 한 **종속성** 보기는 프런트 엔드 ip를 수신기, 규칙 및 백 엔드 풀에 연결 하는 방법을 간략하게 보여 줍니다. 연결 가장자리는 색으로 구분 되며 백 엔드 풀 상태에 따라 추가 세부 정보를 제공 합니다. 이 보기에는 VMSS 및 VM 인스턴스와 같은 관련 된 모든 백 엔드 풀에 대 한 Application Gateway 메트릭과 메트릭의 상세 보기가 제공 됩니다.

![종속성 보기](media/network-insights-overview/dependency-view.png)

종속성 그래프를 사용 하면 구성 설정을 쉽게 탐색할 수 있습니다. 백 엔드 풀을 마우스 오른쪽 단추로 클릭 하 여 다른 기능에 액세스 합니다. 예를 들어 백 엔드 풀이 VM 인 경우 VM Insights에 직접 액세스 하 고 연결 문제를 확인 하는 Network Watcher 연결 문제를 해결할 수 있습니다.

![종속성 보기 메뉴](media/network-insights-overview/dependency-view-menu.png)

종속성 보기의 검색 및 필터 모음은 그래프를 검색 하는 간편한 방법을 제공 합니다. 예를 들어 아래 예제에서 *AppGWTestRule* 를 검색 하면 *AppGWTestRule*를 통해 연결 된 모든 노드로 그래픽 보기의 범위를 좁힐 수 있습니다. 

![검색 예제](media/network-insights-overview/search-example.png)

다른 필터를 통해 특정 경로 및 상태에 대 한 범위를 좁힐 수 있습니다. 예를 들어 상태가 *비정상*인 모든 가장자리를 표시 하려면 상태 드롭다운에서 *비정상* **상태를** 선택 합니다.

Application gateway에 대 한 자세한 메트릭, 모든 백 엔드 풀 리소스 및 프런트 엔드 Ip를 사용 하 여 미리 구성 된 통합 문서를 시작 하려면 **상세 메트릭 보기** 를 클릭 합니다. 

## <a name="next-steps"></a>다음 단계 

- [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)의 네트워크 모니터링에 대해 자세히 알아보세요.
