---
title: Azure 솔루션에서 IoT 장치 모니터링 자습서 | Microsoft Docs
description: 이 자습서에서는 원격 모니터링 솔루션 가속기를 사용하여 IoT 장치를 모니터링하는 방법을 알아봅니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/19/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: d31ea1fe579e5ac7a846c1c0d03012d70be9884d
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159351"
---
# <a name="tutorial-monitor-your-iot-devices"></a>자습서: IoT 장치 모니터링

이 자습서에서는 원격 모니터링 솔루션 가속기를 사용하여 연결된 IoT 장치를 모니터링합니다. 솔루션 대시보드를 사용하여 원격 분석 데이터, 장치 정보, 경고 및 KPI를 볼 수 있습니다.

이 자습서에서는 위치, 속도 및 화물 온도 원격 분석을 전송하는 두 개의 시뮬레이션된 트럭 장치를 사용합니다. 트럭은 Contoso라는 조직에서 관리하고 원격 모니터링 솔루션 가속기에 연결됩니다. Contoso 운영자로서 현장에 있는 트럭 중 하나(truck-02)의 위치 및 동작을 모니터링해야 합니다.

이 자습서에서는 다음을 수행했습니다.

>[!div class="checklist"]
> * 대시보드에서 장치 필터링
> * 실시간 원격 분석 보기
> * 장치 세부 정보 보기
> * 장치에서 경고 보기
> * 시스템 KPI 보기

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [iot-iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>표시할 장치 선택

**대시보드** 페이지에 표시할 연결된 장치를 선택하려면 필터를 사용합니다. **트럭** 장치만 표시하려면 필터 드롭다운 목록에서 기본 제공 **트럭** 필터를 선택합니다.

[![대시보드의 트럭에 대한 필터](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

필터를 적용하면 필터 조건과 일치하는 장치만 **대시보드** 페이지의 맵 및 원격 분석 패널에 표시됩니다. truck-02를 포함한 트럭 두 대가 솔루션 가속기에 연결된 것을 볼 수 있습니다.

[![맵에 트럭만 표시됩니다.](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

필터를 만들고 편집하고 삭제하려면 **장치 그룹 관리**를 클릭합니다.

## <a name="view-real-time-telemetry"></a>실시간 원격 분석 보기

솔루션 가속기는 **대시보드** 페이지의 차트에 실시간 원격 분석 데이터를 표시합니다. 원격 분석 차트의 맨 위에는 truck-02를 포함하여 현재 필터로 선택된 장치에 사용할 수 있는 원격 분석 유형이 표시됩니다. 기본적으로 이 차트는 트럭의 위도를 표시하며 truck-02는 움직이지 않는 것으로 나타납니다.

[![트럭 원격 분석 유형](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

트럭의 온도 원격 분석 데이터를 보려면 **온도**를 클릭합니다. 지난 1시간 동안 truck-02의 온도 변화를 볼 수 있습니다.

[![트럭 온도 원격 분석 표시](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="view-the-map"></a>지도 보기

맵은 현재 필터에 의해 선택된 시뮬레이트된 트럭에 대한 정보를 표시합니다. 맵을 확대/축소 및 이동하여 위치를 더 자세하게 혹은 덜 자세하게 표시할 수 있습니다. 맵의 장치 아이콘 색은 장치에 대해 활성 상태인 **경고**(진한 파랑) 또는 **알림**(빨간색)이 있는지 나타냅니다. **경고** 및 **알림** 수의 요약은 맵의 왼쪽에 표시됩니다.

truck-02의 세부 정보를 보려면 맵을 이동하고 확대/축소하여 truck-02를 찾은 다음, 맵에서 트럭을 선택합니다. 그런 다음, 장치 레이블을 클릭하여 **장치 세부 정보** 패널을 엽니다. 장치 세부 정보에는 다음이 포함됩니다.

* 최근 원격 분석 값
* 장치에서 지원하는 메서드
* 장치 속성

[![대시보드에서 장치 세부 정보 보기](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>경고 보기

**경고** 패널은 장치에서 가장 최근의 경고에 대한 자세한 정보를 표시합니다. truck-02의 경고를 보면 정상적인 화물 온도보다 높다고 표시됩니다.

[![대시보드에서 장치 경고 보기](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

필터를 사용하여 최근 경고에 대한 시간 범위를 조정할 수 있습니다. 기본적으로 패널은 지난 1시간 동안의 경고를 표시합니다.

## <a name="view-the-system-kpis"></a>시스템 KPI 보기

**대시보드** 페이지의 **Analytics** 패널에 솔루션 가속기가 계산한 시스템 KPI가 표시됩니다.

[![대시보드 KPI](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

대시보드에 현재 장치 및 시간 간격 필터로 선택된 경고에 대한 세 가지 KPI가 표시됩니다.

* 가장 많은 경고를 트리거한 규칙에 대한 활성 경고 수입니다.
* 장치 유형별 경고 비율입니다.
* 중요한 경고의 비율입니다.

truck-02의 경우 모든 경고가 정상적인 화물 온도보다 높다는 내용입니다.

경고의 시간 범위를 설정하고 표시할 장치를 제어하는 필터와 동일한 필터가 KPI가 집계되는 방식을 결정합니다. 기본적으로 패널은 지난 1시간에 걸쳐 집계된 KPI를 표시합니다.

[!INCLUDE [iot-iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 원격 모니터링 솔루션 가속기에서 **대시보드** 페이지를 사용하여 시뮬레이션된 트럭을 필터링하고 모니터링하는 방법을 보여줬습니다. 솔루션 가속기를 사용하여 연결된 장치의 문제를 감지하는 방법을 알아보려면 다음 자습서를 계속 진행하십시오.

> [!div class="nextstepaction"]
> [모니터링 솔루션에 연결된 장치를 사용하여 문제 검색](iot-accelerators-remote-monitoring-automate.md)