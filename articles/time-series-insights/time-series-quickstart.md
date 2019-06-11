---
title: '빠른 시작: Azure Time Series Insights 탐색기 | Microsoft Docs'
description: 이 빠른 시작에서는 웹 브라우저에서 간편하게 Azure Time Series Insights 탐색기를 시작하여 다량의 IoT 데이터를 시각화하는 방법을 설명합니다. 데모 환경에서 주요 기능을 둘러봅니다.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: 17aee2555a0d0364ca5eb8e260ae10432a19bce6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244053"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>빠른 시작: Azure Time Series Insights 탐색

이 Azure Time Series Insights 탐색기 빠른 시작에서는 무료 데모 환경에서 Time Series Insights를 시작하는 방법을 안내합니다. 이를 통해 웹 브라우저를 사용하여 대량의 IoT 데이터를 시각화하는 방법을 알아보고, 일반 공급되는 주요 기능을 둘러봅니다.

Azure Time Series Insights는 완전 관리형 분석, 스토리지 및 시각화 서비스이며, 수십억 개의 IoT 이벤트를 동시에 간편하게 탐색 및 분석할 수 있습니다. 데이터에 대한 글로벌 보기를 제공하므로 사용자는 IoT 솔루션의 유효성을 빠르게 검사할 수 있으며, 큰 비용이 수반되는 중요 업무용 디바이스의 가동 중지 시간을 방지할 수 있습니다. Azure Time Series Insights는 거의 실시간으로 숨겨진 추세를 발견하고, 문제를 찾아내고, 근본 원인을 분석할 수 있습니다.

강력한 [REST API](./time-series-insights-update-tsq.md) 및 [클라이언트 SDK](./tutorial-create-tsi-sample-spa.md)를 통해 Azure Time Series Insights를 기존 애플리케이션에 추가하면 유연성을 더욱 높일 수 있습니다. API를 사용하여 원하는 클라이언트 애플리케이션에 시계열 데이터를 저장하고, 쿼리하고, 사용할 수 있습니다. 클라이언트 SDK를 사용하여 기존 애플리케이션에 UI 구성 요소를 추가할 수도 있습니다.

이 Time Series Insights 탐색기 빠른 시작에서는 현재 일반 공급되는 기능을 둘러볼 수 있습니다.

## <a name="prepare-the-demo-environment"></a>데모 환경 준비

1. 아직 Azure 계정이 없는 경우 [Azure 체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

1. 브라우저에서 [일반 공급 데모](https://insights.timeseries.azure.com/demo)로 이동합니다.

1. 메시지가 표시되면 Azure 계정 자격 증명을 사용하여 Time Series Insights 탐색기에 로그인합니다.

1. Time Series Insights 간단한 둘러보기 페이지가 표시됩니다. **다음**을 선택하여 간단한 둘러보기를 시작합니다.

   [![다음 선택](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>데모 환경 살펴보기

1. **시간 선택 패널**이 표시됩니다. 이 패널에서 시각화할 시간 프레임을 선택합니다.

   [![시간 선택 패널](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. 시간 프레임을 선택하여 영역으로 끌어서 놓습니다. **검색**을 선택합니다.

   [![시간 프레임 선택](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   지정한 시간 프레임에 대해 시각화된 차트가 표시됩니다. 꺾은선형 차트 내에서 다양한 작업을 수행할 수 있습니다. 예를 들어 필터링, 고정, 정렬 및 쌓기를 수행할 수 있습니다.

   **시간 선택 패널**로 돌아가려면 다음과 같이 아래쪽 화살표를 선택합니다.

   [![차트](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. **기간 패널**에서 **추가**를 선택하고 새 검색 기간을 추가합니다.

   [![항목 추가](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. 이 차트에서는 특정 지역을 선택하고, 마우스 오른쪽 단추로 클릭한 다음 **이벤트 탐색**을 선택할 수 있습니다.

   [![이벤트 탐색](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   탐색 중인 지역의 원시 데이터가 그리드로 표시됩니다.

   [![그리드 보기](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>데이터 선택 및 필터링

1. 차트의 값을 변경하려면 기간을 편집합니다. 다양한 형식의 값 사이에 어떤 상관 관계가 있는지 확인하려면 또 다른 기간을 추가합니다.

   [![기간 추가](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. **계열 필터링** 상자에 필터 기간을 입력하여 임시 계열 필터링을 수행합니다. 여기서는 **Station5**를 입력하여 해당 측정소의 온도와 기압 사이의 상관관계를 확인합니다.

   [![계열 필터링](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

빠른 시작을 완료했으면 샘플 데이터 집합을 이용하여 다양한 시각화를 수행해 봅니다.

## <a name="next-steps"></a>다음 단계

이제 Time Series Insights 환경을 만들 준비가 완료되었습니다.
> [!div class="nextstepaction"]
> [Time Series Insights 환경 계획](time-series-insights-environment-planning.md)
