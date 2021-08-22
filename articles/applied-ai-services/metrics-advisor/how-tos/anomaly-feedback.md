---
title: Metrics Advisor 서비스에 변칙 피드백 제공
titleSuffix: Azure Cognitive Services
description: Metrics Advisor 인스턴스에서 찾은 변칙에 대한 피드백을 보내고 결과를 조정하는 방법을 알아봅니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbullwin
ms.openlocfilehash: de42785cc2b28e6512db70267961db0b44cf5019
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342156"
---
# <a name="provide-anomaly-feedback"></a>변칙 피드백 제공

사용자 피드백은 변칙 검색 시스템에서 결함을 검색하는 가장 중요한 방법 중 하나입니다. 여기서는 사용자가 시계열에서 잘못된 검색 결과를 직접 표시하고 피드백을 즉시 적용할 수 있는 방법을 제공합니다. 이러한 방식으로 사용자는 활성 상호 작용을 통해 특정 시계열에 대한 변칙 검색을 수행하는 방법을 변칙 검색 시스템에 알려줄 수 있습니다. 

> [!NOTE]
> 현재 피드백은 **스마트 검색** 을 통해서만 변칙 검색 결과에 영향을 주지만 **하드 임계값** 및 **변경 임계값** 은 영향을 주지 않습니다.

## <a name="how-to-give-time-series-feedback"></a>시계열 피드백을 제공하는 방법

모든 계열의 메트릭 세부 정보 페이지에서 피드백을 제공할 수 있습니다. 임의의 포인트를 선택하면 아래 피드백 대화 상자가 표시됩니다. 선택한 계열의 차원이 표시됩니다. 차원 값을 다시 선택하거나 일부를 제거하여 일련의 시계열 데이터를 얻을 수 있습니다. 시계열을 선택한 후 **추가** 단추를 선택하여 피드백을 추가하면 네 가지 종류의 피드백이 제공됩니다. 여러 피드백 항목을 추가하려면 주석을 완료한 후 **저장** 단추를 선택합니다.

:::image type="content" source="../media/feedback/click-on-any-point.png" alt-text="다양한 포인트에서 파란색 선과 빨간색 점이 있는 시계열 데이터 그래프. 다음 텍스트가 있고 빨간색 상자로 둘러싸인 포인트: 임의의 포인트 선택":::

:::image type="content" source="../media/feedback/select-or-remove.png" alt-text="두 개의 차원과, 차원 선택/제거 및 피드백 추가 옵션이 있는 피드백 추가 대화 상자":::

### <a name="mark-the-anomaly-point-type"></a>변칙 포인트 유형 표시

아래 이미지에 표시된 것처럼 피드백 대화 상자는 선택한 포인트의 타임스탬프를 자동으로 채웁니다. 그러나 이 값은 편집할 수 있습니다. 그런 다음 이 항목을 `Anomaly`, `NotAnomaly` 또는 `AutoDetect`으로 식별할지 선택합니다.

:::image type="content" source="../media/feedback/anomaly-value.png" alt-text="Anomaly, NotAnomaly 및 AutoDetect 선택 항목이 있는 드롭다운 메뉴":::

이 선택 항목은 동일한 계열의 향후 변칙 검색 처리에 피드백을 적용합니다. 처리된 포인트는 다시 계산되지 않습니다. 즉, Anomaly를 NotAnomaly로 표시한 경우 향후 유사한 변칙에 대해 경고가 표시되지 않고, `NotAnomaly` 포인트를 `Anomaly`로 표시한 경우 향후 유사한 변칙이 `Anomaly`로 검색됩니다. `AutoDetect`를 선택하면 향후 동일한 포인트에 대한 모든 이전 피드백이 무시됩니다.

## <a name="provide-feedback-for-multiple-continuous-points"></a>여러 연속 포인트에 대한 피드백 제공 

여러 연속 포인트에 대한 변칙 피드백을 동시에 제공하려면 주석을 추가할 포인트 그룹을 선택합니다. 변칙 피드백을 제공하면 선택한 시간 범위가 자동으로 채워지는 것을 볼 수 있습니다.

:::image type="content" source="../media/feedback/continuous-anomaly-feedback.png" alt-text="변칙이 선택되고 특정 시간 범위가 있는 변칙 피드백 메뉴":::

개별 포인트가 변칙 피드백의 영향을 받는지 확인하려면 시계열을 검색할 때 단일 포인트를 선택합니다. 변칙 검색 결과가 피드백에 의해 변경된 경우 도구 설명에 **피드백의 영향을 받음: true** 가 표시됩니다. **피드백의 영향을 받음: false** 가 표시되면 이 포인트에 대해 변칙 피드백 계산이 수행되었지만 변칙 탐지 결과를 변경하지 않아야 한다는 의미입니다.

:::image type="content" source="../media/feedback/affected-by-feedback.png" alt-text="피드백의 영향을 받음: true 텍스트가 빨간색으로 강조 표시된 도구 설명":::

몇 가지 상황에서는 피드백 제공을 제안하지 않습니다.

- 휴일로 인해 변칙이 발생한 경우. 이러한 종류의 거짓 경보를 해결하려면 더 정확한 사전 설정 이벤트를 사용하는 것이 좋습니다.
- 알려진 데이터 원본 변경으로 인해 변칙이 발생한 경우. 예를 들어, 이 시점에 업스트림 시스템 변경이 발생할 수 있습니다. 이 경우 시스템에서 값 변경의 원인이 무엇인지, 그리고 유사한 값 변경이 언제 다시 발생할지 알 수 없으므로 변칙 경고가 나타날 수 있습니다. 따라서 이러한 종류의 문제는 `NotAnomaly`로 주석 처리하지 않는 것이 좋습니다.

## <a name="change-points"></a>포인트 변경

경우에 따라 데이터의 추세 변화가 변칙 검색 결과에 영향을 줄 수 있습니다. 포인트가 변칙인지 여부에 대한 결정을 내릴 때는 기록 데이터의 최신 기간을 고려하게 됩니다. 시계열에 추세 변화가 있는 경우 정확한 변경 포인트를 표시할 수 있습니다. 이렇게 하면 향후 분석에서 변칙 검색에 도움이 됩니다.

아래 그림과 같이 피드백 유형으로 `ChangePoint`를 선택하고 풀다운 목록에서 `ChangePoint`, `NotChangePoint` 또는 `AutoDetect`를 선택할 수 있습니다.

:::image type="content" source="../media/feedback/changepoint.png" alt-text="드롭다운에 ChangePoint, NotChangePoint 및 AutoDetect 옵션을 포함하는 변경 포인트 메뉴":::

> [!NOTE]
> 데이터가 계속 변경되는 경우 한 포인트를 `ChangePoint`로 표시해야 하는 경우 `timerange`를 표시하면 마지막 포인트의 타임스탬프와 시간이 자동으로 채워집니다. 이 경우 주석은 12개 포인트 이후의 변칙 검색 결과에만 영향을 줍니다.

## <a name="seasonality"></a>계절성

계절적인 데이터의 경우 변칙 검색을 수행할 때 한 단계는 시계열의 기간(계절성)을 추정하고 이를 변칙 검색 단계에 적용하는 것입니다. 경우에 따라 정확한 기간을 식별하기가 어려울 수 있으며 기간이 변경될 수도 있습니다. 기간이 잘못 정의되면 변칙 검색 결과에 영향을 줄 수도 있습니다. 도구 설명에서 현재 기간을 찾을 수 있습니다. 이름은 `Min Period`입니다.

:::image type="content" source="../media/feedback/min-period.png" alt-text="기간 및 숫자 7이 빨간색으로 강조 표시된 도구 설명 오버레이":::

이러한 종류의 변칙 검색 오류를 해결하기 위해 기간에 대한 피드백을 제공할 수 있습니다. 그림에 표시된 것처럼 기간 값을 설정할 수 있습니다. 단위 `interval`은 하나의 세분성을 의미합니다. 여기에서 0 간격을 사용하면 데이터가 계절적이지 않음을 의미합니다. 이전 피드백을 취소하고 파이프라인이 기간을 자동으로 검색하도록 하려면 `AutoDetect`를 선택합니다. 
 
> [!NOTE]
> 기간을 설정할 때 타임스탬프 또는 시간 범위를 할당할 필요가 없는 경우 해당 기간은 피드백을 제공하는 순간부터 전체 시계열에서 향후 변칙 검색에 영향을 줍니다.


:::image type="content" source="../media/feedback/period-feedback.png" alt-text="자동 검색 기간이 28로 설정되고 간격이 0(계절적이지 않음을 나타냄)으로 설정된 메뉴":::

## <a name="provide-comment-feedback"></a>설명 피드백 제공

주석을 추가하여 데이터에 대한 설명을 추가하고 컨텍스트를 제공할 수도 있습니다. 설명을 추가하려면 시간 범위를 선택하고 메모에 대한 텍스트를 추가합니다.

:::image type="content" source="../media/feedback/feedback-comment.png" alt-text="시간 범위를 설정하는 기능과 텍스트 기반 설명을 추가하는 상자가 있는 메뉴":::

## <a name="time-series-batch-feedback"></a>시계열 일괄 처리 피드백

앞에서 설명한 것처럼 피드백 모달에서는 차원 값을 다시 선택하거나 제거하여 차원 필터로 정의된 일련의 시계열을 가져올 수 있습니다. 왼쪽 패널에서 피드백에 대해 "+" 단추를 클릭하여 이 모달을 열고 차원 및 차원 값을 선택할 수도 있습니다.

:::image type="content" source="../media/feedback/feedback-time-series.png" alt-text="피드백 단어 옆에 파란색 더하기 기호가 빨간색으로 강조 표시된 메뉴":::

:::image type="content" source="../media/feedback/feedback-dimension.png" alt-text="두 가지 차원이 Dim1 및 Dim2로 표시된 피드백 추가 메뉴":::

## <a name="how-to-view-feedback-history"></a>피드백 기록을 보는 방법

피드백 기록을 보는 방법에는 두 가지가 있습니다. 왼쪽 패널에서 피드백 기록 단추를 선택하면 피드백 목록 모달이 표시됩니다. 단일 계열 또는 차원 필터에 대해 이전에 지정한 모든 피드백이 나열됩니다.

:::image type="content" source="../media/feedback/feedback-history-options.png" alt-text="피드백 목록 메뉴":::

계열에서 피드백 기록을 볼 수도 있습니다. 각 계열의 오른쪽 위 모퉁이에는 여러 단추가 있습니다. 피드백 표시 버튼을 선택하면 해당 선이 변칙 포인트 표시에서 피드백 항목 표시로 전환됩니다. 녹색 플래그는 변경 포인트를 나타내고 파란색 포인트는 다른 피드백 포인트입니다. 이러한 포인트도 선택할 수 있으며 이 포인트에 대해 제공된 피드백의 세부 정보를 나열하는 피드백 목록 모달도 제공됩니다.

:::image type="content" source="../media/feedback/feedback-history-graph.png" alt-text="피드백 기록 그래프":::

:::image type="content" source="../media/feedback/feedback-list.png" alt-text="두 개의 차원이 있는 피드백 목록 메뉴":::

> [!NOTE]
> 메트릭에 액세스할 수 있는 사용자는 누구나 피드백을 제공할 수 있으므로 다른 데이터 피드 소유자가 제공한 피드백을 볼 수 있습니다. 다른 사용자와 동일한 포인트를 편집하는 경우 피드백이 이전 피드백 항목을 덮어씁니다.       

## <a name="next-steps"></a>다음 단계
- [인시던트 진단](diagnose-an-incident.md)
- [메트릭을 구성하고 검색 구성 미세 조정](configure-metrics.md)
- [후크를 사용하여 경고를 구성하고 알림 가져오기](../how-tos/alerts.md)