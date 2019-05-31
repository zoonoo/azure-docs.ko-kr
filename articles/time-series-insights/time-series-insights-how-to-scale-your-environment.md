---
title: Azure Time Series Insights 환경의 크기를 조정하는 방법 | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights 환경의 크기를 조정하는 방법을 설명합니다. Azure Portal을 사용하여 가격 책정 SKU 내에서 용량을 추가하거나 뺍니다.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: be06fd5a6f05d750e6ca9801a6004f7180a12d5c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238984"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Time Series Insights 환경의 크기를 조정하는 방법

이 문서에서는 Azure Portal을 사용하여 Time Series Insights 환경의 환경 용량을 변경하는 방법에 대해 설명합니다. 용량은 선택한 SKU와 관련된 입력 속도, 저장 용량 및 비용에 적용되는 승수입니다.

Azure Portal을 사용하여 지정된 가격 책정 SKU 내에서 용량을 늘리거나 줄일 수 있습니다.

그러나 가격 책정 계층 SKU의 변경은 허용되지 않습니다. 예를 들어, S1 가격 책정 SKU가 있는 환경은 S2로 변환될 수 없으며 그 반대의 경우도 마찬가지입니다.

## <a name="s1-sku-ingress-rates-and-capacities"></a>S1 SKU 수신 속도 및 용량

| S1 SKU 용량 | 수신 속도 | 최대 저장소 용량
| --- | --- | --- |
| 1 | 1GB(1백만 이벤트) | 매달 30GB(3천만 이벤트) |
| 10 | 10GB(1천만 이벤트) | 매달 300GB(3억 이벤트) |

## <a name="s2-sku-ingress-rates-and-capacities"></a>S2 SKU 수신 속도 및 용량

| S2 SKU 용량 | 수신 속도 | 최대 저장소 용량
| --- | --- | --- |
| 1 | 10GB(1천만 이벤트) | 매달 300GB(3억 이벤트) |
| 10 | 100GB(1억 이벤트) | 매달 3TB(30억 이벤트) |

용량은 연속해서 크기가 조정되므로 용량 2의 S1 SKU는 일일 2GB(2백만) 이벤트 수신 속도 및 매달 60GB(6천만 이벤트)를 지원합니다.

## <a name="change-the-capacity-of-your-environment"></a>사용자 환경의 용량 변경

1. Azure Portal에서 Time Series Insights 환경을 찾아 선택합니다.

1. Time Series Insights 환경에 대한 메뉴에서 **구성**을 선택합니다.

   [![configure.png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. **용량** 슬라이더를 조정하여 수신 속도 및 저장소 용량에 대한 요구 사항을 충족하는 용량을 선택합니다. **수신 속도**, **저장소 용량** 및 **예상 비용** 업데이트가 변경으로 인한 영향을 동적으로 보여줍니다.

   [![슬라이더](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   또는 슬라이더의 오른쪽에 있는 텍스트 상자에 용량 승수를 입력할 수 있습니다.

1. **저장**을 선택하여 환경을 확장할 수 있습니다. 진행률 표시기에는 변경 내용이 커밋될 때까지 일시적으로 표시됩니다.

## <a name="next-steps"></a>다음 단계

- 새 용량 인지 확인 [제한을 방지 하기에 충분 한](time-series-insights-diagnose-and-solve-problems.md)합니다.