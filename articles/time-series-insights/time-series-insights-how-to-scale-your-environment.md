---
title: Azure Time Series Insights 환경의 크기를 조정하는 방법 | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights 환경의 크기를 조정하는 방법을 설명합니다. Azure Portal을 사용하여 가격 책정 SKU 내에서 용량을 추가하거나 뺍니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 3f03f5ed75c720c9b0daf30d721ef4d2aee9749c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991150"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Time Series Insights 환경의 크기를 조정하는 방법

이 문서에서는 [Azure Portal](https://portal.azure.com)를 사용 하 여 Time Series Insights 환경의 용량을 변경 하는 방법을 설명 합니다. 용량은 선택한 SKU와 관련된 입력 속도, 스토리지 용량 및 비용에 적용되는 승수입니다.

Azure Portal을 사용하여 지정된 가격 책정 SKU 내에서 용량을 늘리거나 줄일 수 있습니다.

그러나 가격 책정 계층 SKU의 변경은 허용되지 않습니다. 예를 들어, S1 가격 책정 SKU가 있는 환경은 S2로 변환될 수 없으며 그 반대의 경우도 마찬가지입니다.

## <a name="ga-limits"></a>GA 제한

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>사용자 환경의 용량 변경

1. Azure Portal에서 Time Series Insights 환경을 찾아 선택합니다.

1. Time Series Insights 환경에 대한 메뉴에서 **구성**을 선택합니다.

   [.png![구성](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. **용량** 슬라이더를 조정하여 수신 속도 및 스토리지 용량에 대한 요구 사항을 충족하는 용량을 선택합니다. **수신 속도**, **스토리지 용량** 및 **예상 비용** 업데이트가 변경으로 인한 영향을 동적으로 보여줍니다.

   [![슬라이더](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   또는 슬라이더의 오른쪽에 있는 텍스트 상자에 용량 승수를 입력할 수 있습니다.

1. **저장**을 선택하여 환경을 확장할 수 있습니다. 진행률 표시기에는 변경 내용이 커밋될 때까지 일시적으로 표시됩니다.

1. 새 용량이 [제한을 방지 하기에 충분](time-series-insights-diagnose-and-solve-problems.md)한지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Time Series Insights의 보존 기간 이해](time-series-insights-concepts-retention.md)를 참조하세요.

- [Azure Time Series Insights에서 데이터 보존을 구성](time-series-insights-how-to-configure-retention.md)하는 방법에 대해 알아봅니다.

- [환경 계획](time-series-insights-environment-planning.md)에 대해 알아봅니다.