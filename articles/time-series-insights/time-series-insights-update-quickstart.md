---
title: '빠른 시작: Azure Time Series Insights Preview 데모 환경 탐색 | Microsoft Docs'
description: Azure Time Series Insights Preview 데모 환경을 파악합니다.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 12/03/2018
ms.openlocfilehash: de5e853db6c6a0e98dea9251cc07b526288574e1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276835"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>빠른 시작: Azure Time Series Insights Preview 데모 환경 탐색

이 빠른 시작에서는 무료 데모 환경에서 Azure Time Series Insights Preview 탐색기를 사용하는 방법을 보여 줍니다. 웹 브라우저를 사용하여 대용량 산업 IoT 데이터 기록을 시각화하는 방법을 알아보고, Time Series Insights Preview 탐색기의 주요 기능을 둘러봅니다.

Time Series Insights는 엔드투엔드 PaaS(Platform as a Service) 제품을 제공합니다. 향상된 데이터 탐색을 위해 고도로 컨텍스트화되고 시계열에 최적화된 IoT 규모 데이터를 수집, 처리, 저장 및 쿼리할 수 있습니다. 운영 분석도 제공합니다. Time Series Insights는 산업 IoT 배포의 고유한 요구 사항에 맞는 차별화된 제품입니다.

데모 환경에서는 발전 회사인 Contoso를 보여 줍니다. Time Series Insights를 사용하여 Contoso 데이터에서 활용 가능한 인사이트를 찾고 간단하게 근본 원인을 분석합니다. Contoso는 각각 10개의 터빈이 있는 두 개의 풍력 터빈 팜을 운영합니다. 각 터빈에는 1분마다 Azure IoT Hub에 데이터를 보고하는 20개의 센서가 있습니다. 센서는 기상 조건, 블레이드 피치 및 요 위치, 발전기 성능, 기어 박스 동작 및 안전 모니터 관련 정보를 수집합니다.

Time Series Insights Preview를 사용하여 지난 2년간 지속적으로 증가하여 현재 40GB에 달하는 Contoso의 데이터 세트를 분석합니다. 치명적 오류와 이동 속도가 느린 유지 관리 문제를 둘 다 파악하고 예측하는 데 도움이 될 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에  [Azure 체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 을 만듭니다.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>데모 환경에서 Time Series Insights 탐색기 살펴보기

1. 브라우저에서  [ Contoso 풍력 팜 환경](https://insights.timeseries.azure.com/preview/samples)으로 이동합니다.  

1. 메시지가 표시되면 Azure 계정 자격 증명을 사용하여 Time Series Insights 탐색기에 로그인합니다.

### <a name="demo-step-1"></a>데모 단계 1

1. **Contoso Plant 1**의 풍력 터빈 **W7**을 살펴보겠습니다.  

    * **작업**: 보기 범위를 **1/1/17 20:00 - 3/10/17 20:00(UTC)** 으로 업데이트하고 **Contoso Plant 1** > **W7** > **발전기 시스템** > **GeneratorSpeed** 센서를 추가한 다음, 결과 값을 표시합니다.

       ![빠른 시작 1][1]

1. 최근에 Contoso는 풍력 터빈 **W7**에서 화재를 발견했습니다. 여기서 살펴보겠습니다. 화재가 발생하는 동안 화재 경고 센서가 작동된 것을 확인할 수 있습니다.

    * **작업**: 보기 범위를 **3/9/17 20:00 - 3/10/17 20:00(UTC)** 으로 업데이트하고, **안전 시스템** > **FireAlert** 센서를 추가합니다.

      ![빠른 시작 2][2]

1. 화재 당시에 일어난 다른 일을 확인해 보겠습니다. 화재가 발생하기 직전에 오일 압력과 활성 경고가 둘 다 증가했지만, 그때는 문제를 방지하기에 너무 늦었습니다.

    * **작업**: **피치 시스템** > **HydraulicOilPressure** 센서와 **피치 시스템** > **ActiveWarning** 센서를 추가합니다.

      ![빠른 시작 3][3]

1. 뒤로 물러서서 보면 화재로 이어지는 징후가 있었음을 알 수 있습니다. 두 센서가 모두 변동되었습니다. 그렇다면 이전에도 이 문제가 발생했을까요?

    * **작업**: 보기 범위를 **2/24/17 20:00 - 3/10/17 20:00(UTC)** 으로 업데이트합니다.

      ![빠른 시작 4][4]

1. 2년 동안의 모든 데이터를 살펴보면 동일한 징후가 있는 이전 화재 사건을 알 수 있습니다. 이 데이터를 통해 이러한 문제를 조기에 파악할 수 있는 시스템을 빌드할 수 있습니다.

    * **작업**: 보기 범위를 **1/1/16 - 12/31/17**(모든 데이터)로 업데이트합니다.

       ![빠른 시작 5][5]

### <a name="demo-step-2"></a>데모 단계 2

1. 다른 문제는 보다 미묘하고 진단하기 어렵습니다. Time Series Insights는 어려운 문제를 추적하는 데 도움이 되는 다양한 방법을 제공합니다. 여기서는 **6/25**에 **W6**의 경고 센서가 중단된 것을 확인할 수 있습니다. 하지만 실제로 무슨 일이 일어나고 있을까요?

    * **작업**: 현재 센서를 제거하고 보기 범위를 **6/1/17 20:00 - 7/1/17 20:00(UTC)** 으로 업데이트한 다음, **Contoso Plant 1** > **W6** > **안전 시스템** > **VoltageActuatorSwitchWarning** 센서를 추가합니다.

       ![빠른 시작 6][6]

1. 경고는 발전기에서 출력되는 전압에 문제가 있음을 나타냅니다. 그렇다면 원인은 무엇인가요? 세분화된 간격으로 발전기의 전체적인 전력 출력을 살펴보면 괜찮아 보입니다. 그러나 데이터를 집계해 보면 명확한 하락을 확인할 수 있습니다.

    * **작업**: **VoltageActuatorSwitchWarning** 센서를 제거하고 **발전기 시스템** > **ActivePower** 센서를 추가한 다음, 간격을 **3d**로 업데이트합니다.

       ![빠른 시작 7][7]

1. 데이터 세트를 앞으로 탐색하면 이 문제가 일시적인 것이 아님을 확인할 수 있습니다. 문제가 계속 발생하고 있습니다.

    * **작업**: 시간 범위를 오른쪽으로 확장합니다.

       ![빠른 시작 8][8]

1. 자세히 살펴보겠습니다. 다른 센서 데이터 요소를 추가하여 단계별 전압을 확인할 수 있습니다. 그러나 데이터 요소는 모두 비슷해 보입니다. 실제 값을 보기 위해 표식을 놓겠습니다. 3단계 출력에 문제가 있는 것 같습니다.

    * **작업**: **발전기 시스템** > **GridVoltagePhase1**, **GridVoltagePhase2** 및 **GridVoltagePhase3** 센서를 추가합니다. 표시 영역의 마지막 데이터 요소에 마커를 놓습니다.

       ![빠른 시작 8][8]

1. 세 개의 데이터 요소를 모두 같은 비율로 보면 3단계 하락이 훨씬 명확하게 표시됩니다. 이제 경고의 원인을 미리 파악하여 유지 관리 팀에 문제를 알릴 준비가 되었습니다.  

    * **작업**: 동일한 차트 배율로 모든 센서를 오버레이하도록 화면 표시를 업데이트합니다.

       ![빠른 시작 9][9]

## <a name="next-steps"></a>다음 단계

사용자 고유의 Time Series Insights Preview 환경을 만들 준비가 되었습니다.

> [!div class="nextstepaction"]
> [Time Series Insights Preview 환경 계획](time-series-insights-update-plan.md)

<!-- Images -->
[1]: media/v2-update-quickstart/quickstart-one.png
[2]: media/v2-update-quickstart/quickstart-two.png
[3]: media/v2-update-quickstart/quickstart-three.png
[4]: media/v2-update-quickstart/quickstart-four.png
[5]: media/v2-update-quickstart/quickstart-five.png
[6]: media/v2-update-quickstart/quickstart-six.png
[7]: media/v2-update-quickstart/quickstart-seven.png
[8]: media/v2-update-quickstart/quickstart-eight.png
[9]: media/v2-update-quickstart/quickstart-nine.png
