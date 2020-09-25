---
title: Azure Monitor Insights를 사용 하 여 가상 WAN 모니터링
description: 이 문서에서는 Azure Monitor Insights를 사용 하 여 Azure 가상 WAN 모니터링에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 47e9b616a2a686a7ce82a592ee81966041371048
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328006"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>가상 WAN (미리 보기)에 대 한 Azure Monitor 정보

Azure 가상 WAN에 대 한 [Azure Monitor 정보](../azure-monitor/insights/network-insights-overview.md) 를 통해 사용자 및 운영자는 autodiscovered 된 토폴로지 맵을 통해 제공 되는 가상 wan의 상태 및 상태를 볼 수 있습니다. 맵의 리소스 상태 및 상태 오버레이는 가상 WAN의 전반적인 상태에 대 한 스냅숏 보기를 제공 합니다. 가상 WAN 포털의 리소스 구성 페이지에 대 한 클릭 액세스를 통해 맵에서 리소스를 탐색할 수 있습니다.

가상 WAN 리소스 수준 메트릭은 미리 패키지 된 가상 WAN 메트릭 통합 문서를 통해 수집 되 고 표시 됩니다. 통합 문서에는 가상 WAN, 허브, 게이트웨이 및 연결 수준의 메트릭이 표시 됩니다. 이 문서에서는 가상 WAN에 대 한 Azure Monitor Insights를 사용 하 여 가상 WAN 토폴로지와 메트릭을 모두 단일 위치로 보는 단계를 안내 합니다.

> [!NOTE]
> 가상 WAN 포털의 **Insights** 메뉴 옵션이 현재 롤아웃 되 고 있습니다. 이 메뉴가 롤아웃 되는 동안 네트워크에 대 한 Azure Monitor를 사용 하 여 가상 WAN 토폴로지 및 메트릭 통합 문서에 액세스할 수 있습니다. 자세한 내용은 [네트워크에 대 한 Azure Monitor](../azure-monitor/insights/network-insights-overview.md)를 참조 하세요. 
>

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료 하려면 하나 이상의 허브를 사용 하는 가상 WAN이 있어야 합니다. 가상 WAN 및 허브를 만들려면 다음 문서의 단계를 수행 합니다.

* [Virtual WAN 만들기](virtual-wan-site-to-site-portal.md#openvwan)
* [허브 만들기](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>VWAN 토폴로지 보기

**Azure Portal**  >  **가상 WAN**으로 이동 합니다. 왼쪽 창의 **모니터** 메뉴에서 **Insights (미리 보기)** 를 선택 합니다. **Insights** 뷰가 나타납니다. 가상 WAN 종속성 맵과 상위 수준 **메트릭** 미니 통합 문서가 표시 됩니다.

**그림 1: > Insights 메뉴 모니터링**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="Insights 메뉴 옵션을 보여 주는 스크린샷" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

**Insights** 보기에서 Autodiscovered 된 가상 WAN 리소스를 볼 수 있습니다. 이러한 리소스로는 허브, 게이트웨이, 방화벽, 연결 및 스포크 가상 네트워크, 타사 Nva 및 종단 간 가상 WAN의 분기가 있습니다. 예제는 **그림 2**를 참조 하세요.

리소스 상태 및 상태가 지도에서 색으로 구분 되 고 리소스 아이콘에 중첩 됩니다. 허브 용량 및 게이트웨이 사용량과 같은 상위 수준의 가상 WAN 메트릭이 미니 통합 문서의 창 오른쪽에 표시 됩니다.

**그림 2: Insights 보기**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="Insights 보기를 보여 주는 스크린샷" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>종속성 보기

가상 WAN에 대 한 **종속성** 보기는 허브 및 스포크 아키텍처로 광범위 하 게 구성 된 모든 가상 wan 리소스의 상호 연결 된 보기를 시각화 하는 데 도움이 됩니다.

**그림 3: VWAN 종속성 보기**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="종속성 보기를 보여 주는 스크린샷" lightbox="./media/azure-monitor-insights/dependency-map.png":::

**종속성** 보기 맵은 다음 리소스를 연결 된 그래프로 표시 합니다.

* 다양 한 Azure 지역에서 가상 WAN 허브.
* 허브에 직접 연결 된 스포크 가상 네트워크입니다.
* VPN 및 Azure Express 경로 분기 사이트와 각 P2S 사용자는 각 Express 경로, S2S, P2S 연결 및 가상 네트워크 게이트웨이를 통해 각 허브에 연결 된 사용자를 연결 합니다.
* 허브 (보안 허브)에 배포 된 Azure 방화벽 (타사 방화벽 프록시 포함).
* 스포크 가상 네트워크에 배포 된 타사 Nva (네트워크 가상 어플라이언스).

또한 종속성 맵은 간접적으로 연결 된 가상 네트워크 (가상 WAN 스포크 가상 네트워크와 피어 링 되는 가상 네트워크)를 표시 합니다.

종속성 맵은 각 리소스의 구성 설정에 대 한 간편한 탐색을 가능 하 게 합니다. 예를 들어 허브 리소스를 마우스로 가리키면 허브 지역 및 허브 접두사와 같은 기본 리소스 구성을 볼 수 있습니다. 허브 리소스의 Azure Portal 페이지에 액세스 하려면 마우스 오른쪽 단추를 클릭 합니다.

**그림 4: 리소스 관련 정보로 이동**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="리소스 관련 정보를 탐색 하는 방법을 보여 주는 스크린샷":::

**종속성** 보기의 검색 및 필터 모음은 그래프를 검색 하는 쉬운 방법을 제공 합니다. 다양 한 필터를 통해 특정 경로 및 상태로 검색 범위를 좁힐 수 있습니다.

**그림 5: 검색 및 필터링**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="검색 및 필터 표시줄을 보여 주는 스크린샷" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>자세한 메트릭

자세한 메트릭 **보기** 를 선택 하 여 자세한 **메트릭** 페이지에 액세스할 수 있습니다. **메트릭** 페이지는 별도의 탭을 사용 하 여 미리 구성 된 대시보드입니다. 이러한 탭은 가상 wan 수준 및 허브 수준에서 그리고 개별 연결 수준에서 가상 WAN 리소스 용량, 성능 및 사용률에 대 한 통찰력을 제공 합니다.

**그림 6: 상세 메트릭 대시보드**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="자세한 메트릭 대시보드를 보여 주는 스크린샷" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>다음 단계

* 자세히 알아보려면 [Azure Monitor의 메트릭](../azure-monitor/platform/data-platform-metrics.md)을 참조 하세요.
* 모든 가상 WAN 메트릭에 대 한 전체 설명은 [가상 wan 로그 및 메트릭](logs-metrics.md)을 참조 하세요.
