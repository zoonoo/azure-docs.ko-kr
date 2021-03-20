---
title: 메트릭 Advisor 서비스에 변칙 피드백 제공
titleSuffix: Azure Cognitive Services
description: 메트릭 Advisor 인스턴스에서 찾은 변칙에 대 한 피드백을 보내고 결과를 조정 하는 방법을 알아봅니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbullwin
ms.openlocfilehash: 96a6dfebdd0047d5a6f4ff3e295ab96dd8c7226a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184972"
---
# <a name="provide-anomaly-feedback"></a>변칙 피드백 제공

사용자 의견은 변칙 검색 시스템에서 결함을 검색 하는 가장 중요 한 방법 중 하나입니다. 여기서는 사용자가 시계열에서 직접 잘못 된 검색 결과를 표시 하 고 피드백을 즉시 적용할 수 있는 방법을 제공 합니다. 이러한 방식으로 사용자는 활성 상호 작용을 통해 특정 시계열에 대 한 변칙 검색을 수행 하는 방법을 변칙 검색 시스템에 알려줄 수 있습니다. 

> [!NOTE]
> 현재 피드백은 **스마트 검색** 을 통해서만 변칙 검색 결과에 영향을 주지만 **하드 임계값** 및 **변경 임계값** 은 영향을 받지 않습니다.

## <a name="how-to-give-time-series-feedback"></a>시계열 피드백을 제공 하는 방법

모든 시리즈의 메트릭 세부 정보 페이지에서 피드백을 제공할 수 있습니다. 아무 점이 나 선택 하기만 하면 아래 피드백 대화 상자가 표시 됩니다. 선택한 계열의 차원이 표시 됩니다. 차원 값을 다시 선택 하거나 일부를 제거 하 여 시계열 데이터의 일괄 처리를 가져올 수 있습니다. 시계열을 선택한 후 **추가** 단추를 선택 하 여 피드백을 추가 하면 네 가지 종류의 피드백이 제공 됩니다. 여러 피드백 항목을 추가 하려면 주석을 완료 한 후 **저장** 단추를 선택 합니다.

:::image type="content" source="../media/feedback/click-on-any-point.png" alt-text="다양 한 지점에서 파란색 선과 빨간 점이 있는 시계열 데이터를 사용 하는 그래프입니다. 텍스트를 사용 하 여 한 점을 둘러싼 빨간색 상자: 임의의 점을 선택 합니다.":::

:::image type="content" source="../media/feedback/select-or-remove.png" alt-text="두 차원과 차원을 선택/제거 하 고 피드백을 추가 하는 옵션을 사용 하 여 피드백 대화 상자를 추가 합니다.":::

### <a name="mark-the-anomaly-point-type"></a>변칙 점 유형 표시

아래 이미지에 표시 된 것 처럼 사용자 의견 대화 상자는 선택한 지점의 타임 스탬프를 자동으로 채우도록 하지만이 값은 편집할 수 있습니다. 그런 다음, 또는로이 항목을 확인할 지 여부를 `Anomaly` 선택 `NotAnomaly` `AutoDetect` 합니다.

:::image type="content" source="../media/feedback/anomaly-value.png" alt-text="변칙, NotAnomaly 및 자동 검색의 선택 항목이 있는 드롭다운 메뉴":::

이 선택 항목은 동일한 계열의 향후 변칙 검색 처리에 피드백을 적용 합니다. 처리 된 지점은 다시 계산 되지 않습니다. 즉, 변칙을 NotAnomaly으로 표시 하는 경우 앞으로도 유사한 문제가 발생 하지 않으며, 점을로 표시 한 경우 `NotAnomaly` `Anomaly` 앞으로 유사한 점을 검색 하는 경향이 있습니다 `Anomaly` . `AutoDetect`를 선택 하면 나중에 동일한 지점에 대 한 모든 이전 피드백이 무시 됩니다.

## <a name="provide-feedback-for-multiple-continuous-points"></a>여러 연속 요소에 대 한 피드백 제공 

여러 연속 요소에 대 한 변칙 피드백을 동시에 제공 하려는 경우 주석을 추가할 점의 그룹을 선택 합니다. 변칙 피드백을 제공할 때 선택한 시간 범위가 자동으로 채워집니다.

:::image type="content" source="../media/feedback/continuous-anomaly-feedback.png" alt-text="변칙을 선택 하 고 특정 시간 범위를 사용 하는 변칙 피드백 메뉴":::

개별 지점이 변칙 피드백의 영향을 받는지 확인 하려면 시계열을 검색할 때 단일 점을 선택 합니다. 사용자의 변칙 검색 결과가 피드백에 의해 변경 된 경우 도구 설명의 **영향을 받는 사용자 의견: true** 로 표시 됩니다. **사용자 의견의 영향을 받는 것으로** 표시 되는 경우이 점에 대해 변칙 피드백 계산이 수행 되었지만 변칙 검색 결과는 변경 되지 않아야 합니다.

:::image type="content" source="../media/feedback/affected-by-feedback.png" alt-text="사용자 의견의 영향을 받는 단어와 함께 도구 설명이 표시 됩니다. true, 빨간색으로 강조 표시 됩니다.":::

사용자 의견을 제안 하지 않는 몇 가지 상황이 있습니다.

- 비정상은 휴일으로 인 한 것입니다. 이러한 종류의 거짓 경보를 보다 정확 하 게 해결 하기 위해 미리 설정 된 이벤트를 사용 하는 것이 좋습니다.
- 알려진 데이터 원본 변경으로 인해 발생 한 것 같습니다. 예를 들어이 시점에서 업스트림 시스템 변경이 발생 했습니다. 이 경우 시스템은 값 변경의 원인을 알 수 없으며, 유사한 값 변경 사항이 다시 발생 하는 경우에는 변칙 경고를 제공 해야 합니다. 따라서 이러한 종류의 문제에 주석을 추가 하는 것은 제안 하지 않습니다 `NotAnomaly` .

## <a name="change-points"></a>변경 사항

경우에 따라 데이터의 추세 변화가 변칙 검색 결과에 영향을 줍니다. 점이 비정상 인지 여부에 대 한 결정을 내릴 때 기록 데이터의 최신 창이 고려 됩니다. 시계열에 추세 변화가 있는 경우 정확한 변경 지점을 표시할 수 있습니다. 이렇게 하면 향후 분석에서 변칙 탐지기가 도움이 됩니다.

아래 그림에 나와 있는 것 처럼 `ChangePoint` 사용자 의견 유형을 선택 하 고 `ChangePoint` `NotChangePoint` `AutoDetect` 풀 다운 목록에서, 또는를 선택할 수 있습니다.

:::image type="content" source="../media/feedback/changepoint.png" alt-text="ChangePoint, NotChangePoint 및 자동 검색에 대 한 옵션을 포함 하는 드롭다운이 있는 점 메뉴 변경":::

> [!NOTE]
> 데이터가 계속 해 서 변경 되는 경우 한 지점을로 표시 하기만 하면 됩니다 `ChangePoint` . 따라서를 표시 하면 `timerange` 마지막 지점의 타임 스탬프 및 시간이 자동으로 채워집니다. 이 경우 주석은 12 개 요소 후 변칙 검색 결과에만 영향을 줍니다.

## <a name="seasonality"></a>계절성

계절 데이터의 경우 변칙 검색을 수행할 때 시계열의 기간 (계절성)을 추정 하 고 변칙 검색 단계에 적용 하는 단계 중 하나입니다. 경우에 따라 정확한 기간을 식별 하기는 어려울 수 있으며 기간이 변경 될 수도 있습니다. 잘못 정의 된 기간으로 변칙 검색 결과에 부작용이 발생할 수 있습니다. 도구 설명에서 현재 기간을 찾을 수 있습니다. 해당 이름은 `Min Period` 입니다.

:::image type="content" source="../media/feedback/min-period.png" alt-text="빨간색으로 강조 표시 된 단어와 마침표를 사용 하는 도구 설명 오버레이입니다.":::

이러한 종류의 변칙 검색 오류를 해결 하기 위해 기간에 대 한 피드백을 제공할 수 있습니다. 그림에 표시 된 것 처럼 기간 값을 설정할 수 있습니다. 단위는 `interval` 하나의 세분성을 의미 합니다. 여기에서 0 간격을 사용 하면 데이터가 비 계절 임을 의미 합니다. `AutoDetect`이전 피드백을 취소 하 고 파이프라인이 자동으로 기간을 검색 하도록 할 수도 있습니다. 
 
> [!NOTE]
> 기간을 설정할 때 타임 스탬프 또는 timerange를 할당할 필요가 없는 경우 사용자 의견을 제공 하는 순간부터 전체 시계열에 대 한 향후 변칙 검색에 영향을 줍니다.


:::image type="content" source="../media/feedback/period-feedback.png" alt-text="자동 검색 기간이 28로 설정 되 고 interval이 0으로 설정 된 메뉴입니다.":::

## <a name="provide-comment-feedback"></a>의견 피드백 제공

주석을 추가 하 여 데이터에 주석을 추가 하 고 컨텍스트를 제공할 수도 있습니다. 메모를 추가 하려면 시간 범위를 선택 하 고 메모에 대 한 텍스트를 추가 합니다.

:::image type="content" source="../media/feedback/feedback-comment.png" alt-text="텍스트 기반 주석을 추가할 수 있는 시간 범위 및 상자를 설정 하는 기능이 포함 된 메뉴":::

## <a name="time-series-batch-feedback"></a>시계열 일괄 처리 피드백

앞에서 설명한 것 처럼 사용자 의견 모달을 사용 하면 차원 값을 다시 선택 하거나 제거 하 여 차원 필터에서 정의한 시계열 일괄 처리를 가져올 수 있습니다. 왼쪽 패널에서 피드백에 대해 "+" 단추를 클릭 하 고 차원 및 차원 값을 선택 하 여이 모달을 열 수도 있습니다.

:::image type="content" source="../media/feedback/feedback-time-series.png" alt-text="Word 피드백 옆에 빨간색으로 강조 표시 된 파란색 더하기 기호가 있는 메뉴":::

:::image type="content" source="../media/feedback/feedback-dimension.png" alt-text="Dim1 및 Dim2로 표시 되는 두 개의 차원이 있는 피드백 메뉴 추가":::

## <a name="how-to-view-feedback-history"></a>사용자 의견 기록을 보는 방법

사용자 의견 기록을 보는 방법에는 두 가지가 있습니다. 왼쪽 패널에서 피드백 기록 단추를 선택 하면 사용자 의견 목록 모달이 표시 됩니다. 단일 계열 또는 차원 필터에 대해 이전에 지정한 모든 피드백이 나열 됩니다.

:::image type="content" source="../media/feedback/feedback-history-options.png" alt-text="사용자 의견 목록 메뉴":::

사용자 의견 기록을 보는 또 다른 방법은 시리즈의 것입니다. 각 계열의 오른쪽 위 모퉁이에 여러 단추가 표시 됩니다. 피드백 표시 단추를 선택 하면 해당 줄이 변칙 점수를 표시 하 여 피드백 항목을 표시 하는 것으로 전환 됩니다. 녹색 플래그는 변경 지점을 나타내며 파란색 요소는 다른 피드백 지점입니다. 선택할 수도 있습니다. 그러면이 점에 대해 제공 된 피드백의 세부 정보를 나열 하는 피드백 목록 모달을 받게 됩니다.

:::image type="content" source="../media/feedback/feedback-history-graph.png" alt-text="피드백 기록 그래프":::

:::image type="content" source="../media/feedback/feedback-list.png" alt-text="두 개의 차원이 있는 피드백 목록 메뉴":::

> [!NOTE]
> 메트릭에 대 한 액세스 권한이 있는 사용자는 피드백을 제공할 수 있으므로 다른 datafeed 소유자가 제공한 피드백을 볼 수 있습니다. 다른 사용자와 동일한 지점을 편집 하는 경우 피드백은 이전 피드백 항목을 덮어씁니다.       

## <a name="next-steps"></a>다음 단계
- [인시던트를 진단](diagnose-incident.md)합니다.
- [메트릭을 구성하고 구성 감지 미세 조정](configure-metrics.md)
- [후크를 사용하여 경고를 구성하고 알림 가져오기](../how-tos/alerts.md)