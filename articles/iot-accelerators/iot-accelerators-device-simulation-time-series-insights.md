---
title: Time Series Insights로 디바이스 시뮬레이션 원격 분석 시각화 - Azure | Microsoft Docs
description: 디바이스 시뮬레이션 솔루션 가속기에서 생성된 원격 분석을 탐색하고 분석하도록 Time Series Insights 환경을 구성하는 방법을 알아봅니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: aea02cbde32d9485bd49ec39a6f300021c6ef927
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53597701"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Time Series Insights를 사용하여 디바이스 시뮬레이션 솔루션 가속기에서 보낸 원격 분석 시각화

디바이스 시뮬레이션 솔루션 가속기를 사용하여 IoT 솔루션을 테스트하도록 시뮬레이션된 디바이스에서 원격 분석을 생성할 수 있습니다. 이 방법 가이드에서는 Time Series Insights 환경을 사용하여 시뮬레이션된 원격 분석을 시각화 및 분석하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드의 단계를 수행하려면 활성 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 방법 가이드의 단계에서는 Azure 구독에 디바이스 시뮬레이션 솔루션 가속기를 배포했다고 가정합니다. 솔루션 가속기를 배포하지 않은 경우 [클라우드 기반 디바이스 시뮬레이션 솔루션 배포 및 실행](quickstart-device-simulation-deploy.md) 빠른 시작의 단계를 수행합니다.

이 문서에서는 솔루션 가속기의 이름이 **contoso-simulation**이라고 가정합니다. **contoso-simulation**을 다음 단계를 완료할 때 솔루션 가속기의 이름으로 바꿉니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-accelerators-create-tsi.md](../../includes/iot-accelerators-create-tsi.md)]

## <a name="start-a-simulation"></a>시뮬레이션 시작

Time Series Insights 탐색기를 사용하기 전에 일부 원격 분석을 생성하도록 디바이스 시뮬레이션 솔루션 가속기를 구성합니다. 다음 스크린샷에서는 10개의 냉각기 디바이스를 사용하여 실행 중인 시뮬레이션을 보여 줍니다.

![디바이스 시뮬레이션 실행](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights 탐색기

Time Series Insights 탐색기는 원격 분석을 시각화하는 데 사용할 수 있는 웹앱입니다.

1. Azure Portal에서 Time Series Insights **개요** 탭을 선택합니다.

1. Time Series Insights 탐색기 웹앱을 열려면 **환경으로 이동**을 클릭합니다.

    ![Time Series Insights 탐색기](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. 시간 선택 패널의 빠른 시간 메뉴에서 **지난 30분**을 선택하고 **검색**을 클릭합니다.

    ![Time Series Insights 탐색기 검색](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. 왼쪽의 용어 패널에서 **측정값**으로 **온도** 및 **분할 기준** 값으로 **iothub-connection-device-id**를 선택합니다.

    ![Time Series Insights 탐색기 쿼리](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. 차트를 마우스 오른쪽 단추로 클릭하고 **이벤트 탐색**을 선택합니다.

    ![Time Series Insights 탐색기 이벤트](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. 이벤트 데이터는 그리드로 다음을 보여줍니다.

    ![Time Series Insights 탐색기 테이블](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. 큐브 뷰 보기 단추를 클릭합니다.

    ![Time Series Insights 탐색기 큐브 뷰](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. **+** 를 클릭하여 큐브 뷰에 새 쿼리를 추가합니다.

    ![Time Series Insights 탐색기 쿼리 추가](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. 시간 범위로 **지난 30분**, **측정값**으로 **습도** 및 **분할 기준** 값으로 **iothub-connection-device-id**를 선택합니다.

    ![Time Series Insights 탐색기 쿼리](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. 큐브 뷰 보기 단추를 클릭하여 디바이스 원격 분석 대시보드를 봅니다.

    ![Time Series Insights 탐색기 대시보드](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

[!INCLUDE [iot-accelerators-cleanup-tsi.md](../../includes/iot-accelerators-cleanup-tsi.md)]

## <a name="next-steps"></a>다음 단계

Time Series Insights 탐색기에서 데이터를 탐색하고 쿼리하는 방법에 대해 자세히 알아보려면 [Azure Time Series Insights 탐색기](../time-series-insights/time-series-insights-explorer.md)를 참조하세요.
