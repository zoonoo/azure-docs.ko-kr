---
title: Azure 메트릭 탐색기를 사용 하 여 시작
description: Azure 메트릭 탐색기를 사용 하 여 첫 번째 메트릭 차트를 만드는 방법에 알아봅니다.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e611f1564896cfdecb3ce34ab7c5361e5200b48a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60254117"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Azure 메트릭 탐색기를 사용 하 여 시작

## <a name="where-do-i-start"></a>시작 위치
Azure Monitor 메트릭 탐색기는 Microsoft Azure Portal의 구성 요소이며 차트를 그리고, 추세의 상관 관계를 시각적으로 지정하고, 메트릭 값에서 급증 및 하락을 조사할 수 있습니다. 메트릭 탐색기를 사용 하 여 상태 및 리소스 사용률을 조사할 수 있습니다. 다음 순서 대로 시작 합니다.

1. [리소스 및 메트릭을 선택](#create-your-first-metric-chart) 기본 차트를 표시 합니다. 그런 다음 [시간 범위 선택](#select-a-time-range) 조사에 대 한 관련입니다.

1. 시도 [차원 필터를 적용 하 고 분할](#apply-dimension-filters-and-splitting)합니다. 필터 및 분할 어떤 부문이 메트릭의 전체 메트릭 값에 영향을 분석할 수 있습니다 및 가능한 이상 값을 식별 합니다.

1. 사용 하 여 [고급 설정](#advanced-chart-settings-and-next-steps) 차트를 대시보드에 고정 하기 전에 사용자 지정할 수 있습니다. [경고 구성](alerts-metric-overview.md) 메트릭 값을 초과 하거나 임계값 아래로 떨어질 때 알림을 받을 수 있습니다.

## <a name="create-your-first-metric-chart"></a>첫 번째 메트릭 차트 만들기

메트릭 차트에서 리소스, 리소스 그룹, 구독 또는 Azure Monitor 뷰를 만들려면 엽니다는 **메트릭을** 탭 하 고 다음이 단계를 수행:

1. 리소스 선택 기가 사용 메트릭을 보려면 원하는 리소스를 선택 합니다. (연 경우이 리소스는 미리 선택 된 상태로 **메트릭을** 특정 리소스의 컨텍스트에서).

    > ![리소스 선택](./media/metrics-getting-started/resource-picker.png)

2. 일부 리소스에 대 한 네임 스페이스를 선택 해야 합니다. 네임 스페이스는 쉽게 찾을 수 있도록 메트릭을 구성 하는 방법입니다. 예를 들어 저장소 계정에 파일, 테이블, Blob 및 큐 메트릭을 저장 하는 것에 대 한 별도 네임 스페이스입니다. 여러 리소스 종류에는 하나의 네임 스페이스를 하기만 됩니다.

3. 사용 가능한 메트릭 목록에서 메트릭을 선택 합니다.

    > ![메트릭 선택](./media/metrics-getting-started/metric-picker.png)

4. 필요에 따라 메트릭 집계를 변경할 수 있습니다. 예를 들어 메트릭의 최소, 최대 또는 평균 값을 표시 하도록 차트를 좋습니다.

> [!NOTE]
> 사용 된 **메트릭 추가** 단추와 동일한 차트에 표시 되는 여러 메트릭을 보려면 원하는 경우 이러한 단계를 반복 합니다. 하나의 뷰에 여러 차트를 선택 합니다 **차트 추가** 맨 위의 단추를 합니다.

## <a name="select-a-time-range"></a>시간 범위 선택

기본적으로 차트는 메트릭 데이터의 가장 최근 24 시간을 보여줍니다. 사용 하 여는 **시간 선택기** 시간 범위를 변경, 확대 또는 축소 차트에는 패널입니다. 

![변경 시간 범위 패널](./media/metrics-getting-started/time-picker.png)

## <a name="apply-dimension-filters-and-splitting"></a>차원 필터 및 분할 적용

[필터링](metrics-charts.md#apply-filters-to-charts) 하 고 [분할](metrics-charts.md#apply-splitting-to-a-chart) 차원이 포함 된 메트릭에 대 한 강력한 진단 도구입니다. 이러한 다양 한 메트릭 세그먼트 ("차원 값") 영향 메트릭의 전체 값이 표시 기능과 가능한 이상 값을 식별할 수 있습니다.

- **필터링** 차트에 포함 된 차원 값을 선택할 수 있습니다. 차트 작성 하는 경우 성공한 요청 수를 표시 하려는 하는 예를 들어 합니다 *서버 응답 시간* 메트릭. 필터를 적용 해야 합니다 *요청의 성공* 차원입니다. 

- **분할** 차원의 각 값에 대 한 줄 또는 한 줄으로 값을 집계 별도 차트 표시 되는지 여부를 제어 합니다. 예를 들어, 모든 서버 인스턴스에서 평균 응답 시간을 한 줄 보려면 수도 있고 각 서버에 대 한 별도 줄을 볼 수 있습니다. 분리를 적용 해야 합니다 *서버 인스턴스에* 별도 줄을 보려면 차원입니다.

참조 [차트의 예](metric-chart-samples.md) 를 필터링 하 고 분할 적용 합니다. 문서의 단계는 차트를 구성 하는 데 사용 된 보여 줍니다.

## <a name="advanced-chart-settings-and-next-steps"></a>고급 차트 설정 및 다음 단계

제목을 차트 스타일을 사용자 지정할 수 있으며 고급 차트 설정을 수정할 수 있습니다. 사용자 지정을 완료 하는 경우 작업을 저장 하려면 대시보드에 고정 합니다. 또한 메트릭 경고를 구성할 수 있습니다. 따릅니다 [제품 설명서](metrics-charts.md) 이러한 및 기타에 대 한 자세한 고급 Azure Monitor 메트릭 탐색기의 기능입니다.

## <a name="next-steps"></a>다음 단계

* [Azure 서비스에 대 한 사용 가능한 메트릭 목록을 보려면](metrics-supported.md)
* [메트릭 탐색기의 고급 기능에 대해 알아보기](metrics-charts.md)
* [구성 된 차트의 예를 참조 하세요.](metric-chart-samples.md)
