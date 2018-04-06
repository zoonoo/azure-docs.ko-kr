---
title: Stream Analytics 작업 시각화 및 문제 해결 | Microsoft Docs
description: 진단 다이어그램 기능을 사용하여 셀프 서비스 문제 해결을 위해 Stream Analytics 작업 파이프라인을 시각화하는 방법을 알아봅니다.
keywords: ''
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: d87841cd-c59f-4a46-b46e-8b904fdc12e9
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: eae43a6a444514855229af760de6aa1cbec7840a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Stream Analytics 작업 시각화 및 문제 해결
다른 클라우드 기반 기술과 마찬가지로 Stream Analytics에서도 작업이 예상된 출력(또는 해당 문제에 대한 출력)을 생성하지 않는 이유를 알아내기 위해 문제 해결이 필요합니다. 이러한 개념을 고려할 때 Stream Analytics은 스트리밍 작업을 시각화하는 기능을 제공합니다. 이 기능은 모델링 도구만큼 유용하며 업무에 필요한 문서 작성이라는 부수적인 효과도 제공합니다.

시각화 패널에는 실행되는 쿼리뿐만 아니라 입력 내용과 구성된 모든 출력 내용이 표시됩니다. 연결 또는 구성 문제가 좀 더 명확히 드러나며, 구성을 시각적으로 나타내는 것도 도움이 될 수 있습니다.

## <a name="using-the-diagnosis-diagram-tool"></a>진단 다이어그램 도구 사용
이 시각화 도우미에 액세스하려면 Stream Analytics 작업의 "설정" 영역에서 "진단 다이어그램" 단추를 클릭하면 됩니다.

![stream-analytics-troubleshoot-visualization-diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

모든 입력 및 출력은 아래와 같이 해당 구성 요소의 현재 상태를 나타내기 위해 색으로 표시됩니다.

![stream-analytics-troubleshoot-visualization-input-map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

사용자가 작업 내의 데이터 흐름 패턴을 이해하기 위해 중간 쿼리 단계를 확인하려는 경우 시각화 도구에 쿼리가 구성 요소 단계 및 흐름 시퀀스로 구분되어 표시됩니다. 각 쿼리 단계를 클릭하면 그림과 같이 쿼리 편집 창에 해당 섹션이 표시됩니다. 

![stream-analytics-troubleshoot-visualization-intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

