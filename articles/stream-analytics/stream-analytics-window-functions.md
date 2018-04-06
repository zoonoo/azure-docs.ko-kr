---
title: Stream Analytics 창 함수 소개 | Microsoft Docs
description: Stream Analytics의 세 가지 창 함수(연속, 도약, 슬라이딩)에 대해 알아봅니다.
keywords: 연속 창, 슬라이딩 윈도우, 도약 창
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: 0d8d8717-5d23-43f0-b475-af078ab4627d
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: 3757834ff816fffc4571aeef8c164c2230c0f7c2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="introduction-to-stream-analytics-window-functions"></a>Stream Analytics 창 함수 소개
많은 실시간 스트리밍 시나리오에서 임시 창에 포함된 데이터에 작업을 수행하는 데 필요합니다. 창 함수의 네이티브 지원은 개발자가 복잡한 스트림 처리 작업을 작성할 때 생산성을 향상시키는 Azure Stream Analytics의 주요 함수입니다. Stream Analytics을 사용하면 개발자가 [**연속**](https://msdn.microsoft.com/library/dn835055.aspx), [**도약**](https://msdn.microsoft.com/library/dn835041.aspx) 및 [**슬라이딩**](https://msdn.microsoft.com/library/dn835051.aspx) 창을 사용하여 스트리밍 데이터에 대한 임시 작업을 수행할 수 있습니다. 모든 [창](https://msdn.microsoft.com/library/dn835019.aspx) 작업 결과가 창의 **끝** 에 출력됩니다. 창의 출력은 사용된 집계 함수를 기반으로 하는 단일 이벤트입니다. 이벤트에는 창 끝의 타임스탬프가 있고 모든 창 함수는 고정된 길이로 정의됩니다. 마지막으로 모든 창 함수는 반드시 [**GROUP BY**](https://msdn.microsoft.com/library/dn835023.aspx) 절에서 사용되어야 합니다.

![Stream Analytics 창 함수 개념](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>연속 창
연속 창 함수는 다른 시간 세그먼트에 데이터 스트림을 분할하는 데 사용되고 아래 예와 같이 그에 대한 기능을 수행합니다. 연속 창의 핵심적인 차이는 반복되지만 겹치지 않고 이벤트가 둘 이상의 연속 창에 속할 수 없다는 점입니다.

![Stream Analytics 창 함수 연속 소개](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>도약 창
도약 창 함수는 고정된 기간만큼 시간을 앞으로 건너 뜁니다. 겹칠 수 있는 연속 창으로 생각하기 쉬우므로 이벤트는 둘 이상의 도약 창 결과 집합에 속할 수 있습니다. 연속 창과 동일한 도약 창을 만들려면 도약 크기를 창 크기와 동일하도록 지정하면 됩니다. 

![Stream Analytics 창 함수 도약 소개](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>슬라이딩 윈도우
연속 또는 도약 창과 달리 슬라이딩 윈도우 함수는 이벤트가 발생할 때 출력 **만** 생성합니다. 모든 창에는 하나 이상의 이벤트가 있고 창은 지속적으로 €(엡실론)만큼 앞으로 이동합니다. 도약 창과 마찬가지로 이벤트는 둘 이상의 슬라이딩 윈도우에 속할 수 있습니다.

![Stream Analytics 창 함수 슬라이딩 소개](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>창 함수 관련 도움말 보기
추가 지원이 필요할 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

