---
title: Azure 메트릭 탐색기 시작
description: Azure 메트릭 탐색기를 사용하여 처음으로 메트릭 차트를 만드는 방법을 알아봅니다.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 920ff19b162843d1c2d631ad192ce1b3c4273a68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248776"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Azure 메트릭 탐색기 시작

## <a name="where-do-i-start"></a>어디서부터 시작해야 하나요?
Azure Monitor 메트릭 탐색기는 Microsoft Azure Portal의 구성 요소이며 차트를 그리고, 추세의 상관 관계를 시각적으로 지정하고, 메트릭 값에서 급증 및 하락을 조사할 수 있습니다. 메트릭 탐색기를 사용하여 리소스의 상태 및 사용률을 조사합니다. 다음 순서로 시작합니다.

1. [리소스와 메트릭을 선택하면](#create-your-first-metric-chart) 기본 차트가 표시됩니다. 그런 다음 [조사와](#select-a-time-range) 관련된 시간 범위를 선택합니다.

1. [차원 필터를 적용하고 분할해](#apply-dimension-filters-and-splitting)보십시오. 필터 및 분할을 사용하면 전체 메트릭 값에 기여하는 메트릭 세그먼트를 분석하고 가능한 이상값을 식별할 수 있습니다.

1. [고급 설정을](#advanced-chart-settings) 사용하여 대시보드에 고정하기 전에 차트를 사용자 지정합니다. 메트릭 값이 [임계값을](alerts-metric-overview.md) 초과하거나 아래로 떨어질 때 알림을 수신하도록 경고를 구성합니다.

## <a name="create-your-first-metric-chart"></a>첫 번째 메트릭 차트 만들기

리소스, 리소스 그룹, 구독 또는 Azure Monitor 보기에서 메트릭 차트를 만들려면 **메트릭 탭을** 열고 다음 단계를 따르십시오.

1. 리소스 선택기를 사용하여 메트릭을 볼 리소스를 선택합니다. (특정 리소스의 컨텍스트에서 메트릭을 연 경우 **리소스가** 미리 선택됩니다.)

    > ![리소스 선택](./media/metrics-getting-started/resource-picker.png)

2. 일부 리소스의 경우 네임스페이스를 선택해야 합니다. 네임스페이스는 메트릭을 쉽게 찾을 수 있도록 구성하는 방법일 뿐입니다. 예를 들어 스토리지 계정에는 파일, 테이블, Blob 및 큐 메트릭을 저장하기 위한 별도의 네임스페이스가 있습니다. 여러 리소스 유형 중 대부분은 네임스페이스가 하나만 있습니다.

3. 사용 가능한 메트릭 목록에서 메트릭을 선택합니다.

    > ![메트릭 선택](./media/metrics-getting-started/metric-picker.png)

4. 선택적으로 메트릭 집계를 변경할 수 있습니다. 예를 들어 차트에 메트릭의 최소값, 최대값 또는 평균 값을 표시하도록 할 수 있습니다.

> [!NOTE]
> 동일한 차트에 여러 메트릭을 표시하려면 **메트릭 추가** 단추를 사용하여 이 단계를 반복합니다. 한 뷰에서 여러 차트의 경우 상단에 있는 **차트 추가** 단추를 선택합니다.

## <a name="select-a-time-range"></a>시간 범위 선택

기본적으로 차트는 최근 24시간 동안의 메트릭 데이터를 표시합니다. 시간 **선택기** 패널을 사용하여 차트에서 시간 범위를 변경하거나 확대하거나 축소할 수 있습니다. 

![시간 범위 변경 패널](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> 시간 **브러시를** 사용하여 차트의 흥미로운 영역(스파이크 또는 딥)을 조사합니다. 영역의 시작 부분에 마우스 포인터를 놓고 왼쪽 마우스 버튼을 길게 누를 때 영역의 다른 쪽으로 드래그한 다음 단추를 놓습니다. 그러면 해당 시간 범위에서 차트가 확대됩니다. 

## <a name="apply-dimension-filters-and-splitting"></a>차원 필터 및 분할 적용

[필터링](metrics-charts.md#apply-filters-to-charts) 및 [분할은](metrics-charts.md#apply-splitting-to-a-chart) 차원이 있는 메트릭에 대한 강력한 진단 도구입니다. 이러한 기능은 다양한 측정항목("측정기준 값")이 메트릭의 전체 값에 미치는 영향을 보여 주며 가능한 이상값을 식별할 수 있도록 합니다.

- **필터링**을 사용하여 차트에 포함할 차원 값을 선택할 수 있습니다. 예를 들어 *서버 응답 시간* 메트릭을 차트로 표시할 때 성공적인 요청을 표시할 수 있습니다. *요청 차원의 성공* 사례에 필터를 적용해야 합니다. 

- **분할**은 차원의 각 값에 대한 별도의 줄을 차트에 표시할 것인지 아니면 값을 한 줄로 집계할 것인지 여부를 제어합니다. 예를 들어 모든 서버 인스턴스에서 평균 응답 시간에 대해 한 줄을 보거나 각 서버에 대해 별도의 줄을 볼 수 있습니다. 별도의 줄을 보려면 서버 *인스턴스* 차원에 분할을 적용해야 합니다.

필터링 및 분할이 적용된 차트는 [차트 예제](metric-chart-samples.md)를 참조하세요. 이 문서에서는 차트를 구성하는 데 사용된 단계를 보여 주어 있습니다.

## <a name="advanced-chart-settings"></a>고급 차트 설정

차트 스타일 및 제목을 사용자 지정하고 고급 차트 설정을 수정할 수 있습니다. 사용자 지정을 마쳤으면 대시보드에 고정하여 작업을 저장합니다. 메트릭 경고를 구성할 수도 있습니다. [제품 설명서를](metrics-charts.md) 따라 Azure Monitor 메트릭 탐색기의 이러한 기능 및 기타 고급 기능에 대해 알아봅니다.

## <a name="next-steps"></a>다음 단계

* [메트릭 탐색기의 고급 기능에 대해 알아보기](metrics-charts.md)
* [메트릭 탐색기 문제 해결](metrics-troubleshoot.md)
* [Azure 서비스에 사용 가능한 메트릭 목록 보기](metrics-supported.md)
* [구성된 차트 예제 보기](metric-chart-samples.md)
