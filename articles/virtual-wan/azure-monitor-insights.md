---
title: Azure Monitor Insights를 사용 하 여 Azure 가상 WAN 모니터링
description: Azure Monitor Insights를 사용 하 여 가상 WAN 모니터링에 대 한 자세한 정보
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: cherylmc
ms.openlocfilehash: 8553a809173d955a21e6730de35c70de5b69e81b
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136260"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>가상 WAN (미리 보기)에 대 한 Azure Monitor 정보

가상 WAN에 대 한 [Azure Monitor 정보](../azure-monitor/insights/network-insights-overview.md) 를 통해 사용자 및 운영자는 autodiscovered 된 토폴로지 맵을 통해 제공 되는 가상 wan의 상태 및 상태를 볼 수 있습니다. 리소스 상태 및 상태는 가상 WAN의 전반적인 상태에 대 한 스냅숏 보기를 제공 하기 위해 맵에 중첩 됩니다. 리소스 탐색은 가상 WAN 포털의 리소스 구성 페이지에 대 한 클릭 액세스를 통해 맵에서 사용 하도록 설정 됩니다.

가상 wan, 허브, 게이트웨이 및 연결 수준의 메트릭을 표시 하는 미리 패키지 된 가상 WAN 메트릭 통합 문서를 통해 가상 WAN 리소스 수준 메트릭이 수집 되 고 표시 됩니다. 이 문서에서는 가상 WAN에 대 한 Azure Monitor Insights를 사용 하 여 가상 WAN 토폴로지와 메트릭을 모두 단일 위치로 보는 단계를 안내 합니다.

> [!NOTE]
> Insights 메뉴가이 미리 보기에 대해 롤아웃 되는 중입니다.
>

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계에서는 하나 이상의 허브가 있는 Virtual WAN을 이미 배포했다고 가정합니다. 새 Virtual WAN 및 새 허브를 만들려면 다음 문서의 단계를 수행합니다.

* [Virtual WAN 만들기](virtual-wan-site-to-site-portal.md#openvwan)
* [허브 만들기](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>VWAN 토폴로지 보기

**Azure Portal->가상 WAN**의 왼쪽에 있는 **모니터** 메뉴에서 **Insights (미리 보기)** 를 선택 합니다. 그러면 가상 WAN 종속성 맵과 상위 수준 메트릭 미니 통합 문서가 표시 되는 **Insights 보기가**표시 됩니다.

**그림 1: 모니터-Insights 메뉴**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="숫자" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

**정보** 보기에서 **그림 2**에 표시 된 것 처럼 종단 간 가상 wan에서 허브, 게이트웨이, 방화벽, 연결 및 스포크 vnet, 타사 nva, 분기 등의 자동 검색 된 가상 wan 리소스를 볼 수 있습니다.

**리소스 상태** 및 **상태가** 지도에서 색으로 구분 되 고 리소스 아이콘에 중첩 됩니다. 허브 용량과 게이트웨이 사용량과 같은 가상 WAN 상위 수준 메트릭이 미니 통합 문서를 통해 오른쪽에 표시 됩니다.

**그림 2: Insights 보기**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="숫자" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>종속성 보기

가상 WAN에 대 한 **종속성** 보기는 허브 및 스포크 아키텍처로 광범위 하 게 구성 된 모든 가상 wan 리소스의 상호 연결 된 보기를 시각화 하는 데 도움이 됩니다.

**그림 3: VWAN 종속성 보기**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="종속성 맵" lightbox="./media/azure-monitor-insights/dependency-map.png":::

종속성 보기 맵은 다음 리소스를 연결 된 그래프로 표시 합니다.

* 다양 한 Azure 지역에서 가상 WAN 허브.
* 허브에 직접 연결 된 스포크 Vnet.
* VPN 및 Express 경로 지점 사이트 및 P2S 사용자는 각 Express 경로, S2S 및 P2S 연결 및 가상 네트워크 게이트웨이를 통해 각 허브에 연결 됩니다.
* 허브 (보안 허브)에 배포 된 Azure 방화벽 (타사 방화벽 프록시 포함).
* 스포크 Vnet 배포 된 타사 NVA (네트워크 가상 어플라이언스).

또한 종속성 맵은 간접적으로 연결 된 Vnet (가상 WAN 스포크 Vnet와 피어 링 VNet)를 표시 합니다.

종속성 맵은 각 리소스의 구성 설정에 대 한 간편한 탐색을 가능 하 게 합니다. 예를 들어 허브 리소스를 마우스로 가리켜 허브 지역 및 허브 접두사와 같은 기본 리소스 구성을 볼 수 있습니다. 허브 리소스의 Azure Portal 페이지에 액세스 하려면 마우스 오른쪽 단추를 클릭 합니다.

**그림 4: 리소스 관련 정보로 이동**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="리소스 정보":::

종속성 보기의 검색 및 필터 모음은 그래프를 검색 하는 간편한 방법을 제공 합니다. 다른 필터를 통해 특정 경로 및 상태로 검색 범위를 좁힐 수 있습니다.

**그림 5: 검색 및 필터링**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="검색 및 필터 표시줄" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>자세한 메트릭

자세한 메트릭 **보기** 를 선택 하 여 자세한 **메트릭** 페이지에 액세스할 수 있습니다. 메트릭 페이지는 가상 wan 수준, 허브 수준 및 개별 연결의 가상 WAN 리소스 용량, 성능 및 사용률에 대 한 유용한 정보를 제공 하는 별도의 탭으로 미리 구성 된 대시보드입니다.

**그림 6: 상세 메트릭 대시보드**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="자세한 메트릭" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>다음 단계

* Azure Monitor의 메트릭에 대 한 자세한 내용은 [Azure Monitor의 메트릭](../azure-monitor/platform/data-platform-metrics.md)을 참조 하세요.
* 모든 가상 WAN 메트릭에 대 한 전체 설명은 [가상 wan 로그 및 메트릭](logs-metrics.md)을 참조 하세요.
