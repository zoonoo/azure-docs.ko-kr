---
title: 경고에 대한 근본 원인 분석 수행 - Azure | Microsoft Docs
description: 이 자습서에서는 Azure Time Series Insights를 사용하여 경고에 대한 근본 원인 분석을 수행하는 방법을 알아봅니다.
author: aditidugar
ms.author: adugar
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 70d29359d4a4bcf9f5badbbf0c553d7bed88a02b
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284568"
---
# <a name="tutorial-conduct-a-root-cause-analysis-on-an-alert"></a>자습서: 경고에 대한 근본 원인 분석 수행

이 자습서에서는 원격 모니터링 솔루션 가속기를 사용하여 경고의 근본 원인을 진단하는 방법을 알아봅니다. 원격 모니터링 솔루션 대시보드에서 경고가 트리거되었음을 확인한 다음, Azure Time Series Insights 탐색기를 사용하여 근본 원인을 조사합니다.

이 자습서에서는 위치, 고도, 속도 및 화물 온도 원격 분석을 전송하는 두 개의 시뮬레이션된 배달 트럭 장치를 사용합니다. 트럭은 Contoso라는 조직에서 관리하고 원격 모니터링 솔루션 가속기에 연결됩니다. Contoso 기사의 경우 트럭 중 하나(delivery-truck-02)가 온도 저하 경고를 기록한 이유를 이해해야 합니다.

이 자습서에서는 다음을 수행했습니다.

>[!div class="checklist"]
> * 대시보드에서 장치 필터링
> * 실시간 원격 분석 보기
> * Time Series Insights 탐색기에서 데이터 탐색
> * 근본 원인 분석 수행
> * 학습 내용을 기반으로 새 규칙 만들기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>표시할 장치 선택

**대시보드** 페이지에 표시할 연결된 장치를 선택하려면 필터를 사용합니다. **트럭** 장치만 표시하려면 필터 드롭다운 목록에서 기본 제공 **트럭** 필터를 선택합니다.

[![대시보드의 트럭에 대한 필터](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-expanded.png#lightbox)

필터를 적용하면 필터 조건과 일치하는 디바이스만 **대시보드**의 맵 및 원격 분석 패널에 표시됩니다. **truck-02**를 포함한 트럭 두 대가 솔루션 가속기에 연결된 것을 볼 수 있습니다.

## <a name="view-real-time-telemetry"></a>실시간 원격 분석 보기

솔루션 가속기는 **대시보드** 페이지의 차트에 실시간 원격 분석 데이터를 표시합니다. 기본적으로 차트는 시간에 따라 달라지는 고도 원격 분석을 보여 줍니다.

[![트럭 고도 원격 분석 그림](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-expanded.png#lightbox)

트럭의 온도 원격 분석 데이터를 보려면 **원격 분석 패널**에서 **온도**를 클릭합니다. 지난 15분 동안 두 트럭의 온도 변화를 볼 수 있습니다. 경고 창에서 delivery-truck-02에 대한 온도 저하 경고가 트리거되었음을 알 수도 있습니다.

[![온도 저하 경고가 표시된 RM 대시보드](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-expanded.png#lightbox)

## <a name="explore-the-data"></a>데이터 탐색

낮은 온도 경보의 원인을 식별하려면 Time Series Insights 탐색기에서 제공 트럭 원격 분석 데이터를 엽니다. 대시보드에서 **Time Series Insights에서 탐색**을 클릭합니다.

[![TSI 링크가 강조 표시되어 있는 RM 대시보드](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-expanded.png#lightbox)

탐색기가 실행되면 나열된 모든 장치가 표시됩니다.

[![TSI 탐색기 초기 보기](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-expanded.png#lightbox)

필터 상자에 **delivery-truck**을 입력하여 디바이스를 필터링하고 왼쪽 패널에서 **측정값**으로 **온도**를 선택합니다.

[![TSI 탐색기 트럭 온도](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

원격 모니터링 대시보드에서 본 것과 동일한 보기가 표시됩니다. 또한 경고가 트리거된 시간 프레임을 좀 더 자세히 확대할 수도 있습니다.

[![TSI 탐색기 확대/축소](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

트럭에서 나오는 다른 원격 분석 스트림에서 추가할 수도 있습니다. 맨 위 왼쪽 모서리의 **추가** 단추를 클릭합니다. 새 창이 나타납니다.

[![새 분할 창이 있는 TSI 탐색기](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

새 분할 창에서 새 레이블의 이름을 **장치**로 변경하여 이전 레이블과 일치하도록 하세요. **측정값**으로 **고도**를, **분할 기준**으로 **iothub-connection-device-id**를 선택하여 고도 원격 분석을 보기에 추가합니다.

[![온도 및 고도가 표시된 TSI 탐색기](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>경고 진단

현재 보기에서 스트림을 보면 두 트럭에 대한 고도 프로필이 서로 다르다는 것을 확인할 수 있습니다. 또한 트럭이 높은 고도로 도달하면 **delivery-truck-02**의 온도 저하가 발생합니다. 트럭들은 동일한 경로를 사용하도록 예정되었기 때문에 이 발견은 놀랍습니다.

트럭이 서로 다른 이동 경로를 사용했다는 의혹을 확인하려면 **추가** 단추를 사용하여 가로 패널에 다른 창을 추가합니다. 새 분할 창에서 새 레이블의 이름을 **장치**로 변경하여 이전 레이블과 일치하도록 하세요. **측정값**으로 **경도**를, **분할 기준**으로 **iothub-connection-device-id**를 선택하여 경도 원격 분석을 보기에 추가합니다. **경도** 스트림 간의 차이를 보고 서로 다른 이동 경로를 따랐음을 알 수 있습니다.

[![온도, 고도 및 경도가 표시된 TSI 탐색기](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>새 규칙 만들기

트럭 경로는 일반적으로 사전에 최적화되어 있지만, 교통 패턴, 날씨 및 기타 예측할 수 없는 사건으로 인해 지연이 발생하고 결국 가장 적절한 판단을 기반으로 트럭 운전사가 마지막 순간 경로를 결정해야 할 수 있습니다. 그러나 차량 내의 자산 온도는 치명적이므로 원격 모니터링 솔루션에 추가 규칙을 작성해야 합니다. 1분 동안의 평균 고도가 350피트를 넘는 경우에는 경고를 수신하도록 이 규칙을 지정해야 합니다.

[![원격 모니터링 규칙 탭 설정 고도 규칙](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

규칙을 작성하고 편집하는 방법을 알려면 [장치 문제 검색](iot-accelerators-remote-monitoring-automate.md)에 대한 이전 자습서를 확인하세요.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 원격 모니터링 솔루션 가속기와 함께 Time Series Insights 탐색기를 사용하여 경고의 근본 원인을 진단하는 방법을 보여 줍니다. 솔루션 가속기를 사용하여 연결된 장치의 문제를 확인하고 해결하는 방법을 알아보려면 다음 자습서를 계속 진행하십시오.

> [!div class="nextstepaction"]
> [장치 경고를 사용하여 모니터링 솔루션에 연결된 장치의 문제를 확인 및 해결](iot-accelerators-remote-monitoring-maintain.md)
