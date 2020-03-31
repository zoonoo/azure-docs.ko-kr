---
title: 환경 확장 방법 - Azure 타임시리즈 인사이트| 마이크로 소프트 문서
description: Azure 포털을 사용하여 Azure Time Series Insights 환경을 확장하는 방법을 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 23efda2793ef5d323089ee5b72fb1ea873de6b20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310988"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Time Series Insights 환경의 크기를 조정하는 방법

이 문서에서는 [Azure 포털을](https://portal.azure.com)사용하여 열렬 인사이트 환경의 용량을 변경하는 방법에 대해 설명합니다. 용량은 선택한 SKU와 관련된 입력 속도, 스토리지 용량 및 비용에 적용되는 승수입니다.

Azure Portal을 사용하여 지정된 가격 책정 SKU 내에서 용량을 늘리거나 줄일 수 있습니다.

그러나 가격 책정 계층 SKU의 변경은 허용되지 않습니다. 예를 들어, S1 가격 책정 SKU가 있는 환경은 S2로 변환될 수 없으며 그 반대의 경우도 마찬가지입니다.

## <a name="ga-limits"></a>GA 한도

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>사용자 환경의 용량 변경

1. Azure Portal에서 Time Series Insights 환경을 찾아 선택합니다.

1. [열계] 인사이트 환경의 메뉴에서 **저장소 구성을**선택합니다.

   [![타임시리즈 인사이트 용량 구성](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. **용량** 슬라이더를 조정하여 수신 속도 및 스토리지 용량에 대한 요구 사항을 충족하는 용량을 선택합니다. **수신 속도**, **스토리지 용량** 및 **예상 비용** 업데이트가 변경으로 인한 영향을 동적으로 보여줍니다.

   [![용량 슬라이더를 사용하여 환경 구성](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   또는 슬라이더의 오른쪽에 있는 텍스트 상자에 용량 승수를 입력할 수 있습니다.

1. **저장**을 선택하여 환경을 확장할 수 있습니다. 진행률 표시기에는 변경 내용이 커밋될 때까지 일시적으로 표시됩니다.

1. 새 용량이 [제한을 방지하기에 충분한지](time-series-insights-diagnose-and-solve-problems.md)확인합니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Time Series Insights의 보존 기간 이해](time-series-insights-concepts-retention.md)를 참조하세요.

- Azure [Time Series Insights에서 데이터 보존 구성에](time-series-insights-how-to-configure-retention.md)대해 자세히 알아봅니다.

- 환경 [계획에](time-series-insights-environment-planning.md)대해 자세히 알아봅니다.