---
title: "Azure Time Series Insights 환경의 크기를 조정하는 방법 | Microsoft Docs"
description: "이 자습서에서는 Azure Time Series Insights 환경의 크기를 조정하는 방법을 다룹니다."
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 8f6c66ea2173c98179ec899d6626c2ab6f7ec4b6
ms.contentlocale: ko-kr
ms.lasthandoff: 05/01/2017

---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Time Series Insights 환경의 크기를 조정하는 방법

이 자습서에서는 Time Series Insights 환경의 크기를 조정하는 방법을 다룹니다.

> [!NOTE]
> SKU 형식 전반에서의 강화는 허용되지 않습니다. S1 SKU를 사용한 환경은 S2 환경으로 변환할 수 없습니다.

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

## <a name="changing-the-capacity-of-your-environment"></a>사용자 환경의 용량 변경

1. Azure Portal에서 용량을 변경할 환경을 선택합니다.
1. 설정에서 구성을 클릭합니다.
1. 용량 슬라이더를 사용하여 수신 속도 및 저장소 용량에 대한 요구 사항을 충족하는 용량을 선택합니다.

## <a name="next-steps"></a>다음 단계

* 새 용량이 제한을 방지하기에 충분한지 확인합니다. 자세한 내용은 [여기](time-series-insights-diagnose-and-solve-problems.md)에서 *사용자 환경이 제한될 수 있습니다* 섹션을 참조하세요.
