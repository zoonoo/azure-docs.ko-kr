---
title: "Azure Stream Analytics: 스트리밍 단위를 효율적으로 사용하도록 작업을 최적화 | Microsoft Docs"
description: "Azure Stream Analytics의 크기 조정 및 확장에 대한 모범 사례를 쿼리합니다."
keywords: "스트리밍 단위, 쿼리 성능"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 341d4f057e0a4611bbff498d494ca54d0b1fd9e7
ms.contentlocale: ko-kr
ms.lasthandoff: 07/04/2017


---

# <a name="optimize-your-job-to-use-streaming-units-efficiently"></a>스트리밍 단위를 효율적으로 사용하도록 작업을 최적화

Azure Stream Analytics는 실행하는 작업의 성능 "가중치"를 SU(스트림 단위)에 집계합니다. SU는 작업을 실행하는 데 사용하는 컴퓨팅 리소스를 나타냅니다. SU는 CPU, 메모리의 혼합된 측정치 및 읽기/쓰기 속도를 기반으로 상대적 이벤트 처리 용량을 설명하는 방법을 제공합니다. 이 용량을 통해 사용자는 쿼리 논리에 집중할 수 있습니다. 또한 사용자가 저장소 계층 성능 고려 사항에 대해 알고, 작업에 대한 메모리를 수동으로 할당하고, 작업을 시기적절하게 실행하는 데 필요한 CPU 코어 수를 예상할 필요가 없어집니다.

## <a name="how-many-sus-are-required-for-a-job"></a>작업에 필요한 SU 수는?

특정 작업에 필요한 SU 수 선택은 입력에 대한 파티션 구성 및 작업에 정의된 쿼리에 따라 달라집니다. **규모** 블레이드를 사용하면 올바른 SU 수를 설정할 수 있습니다. 필요한 것보다 많은 SU를 할당하는 것이 좋습니다. Stream Analytics 처리 엔진은 메모리를 추가로 할당하는 비용으로 대기 시간과 처리량을 최적화합니다.

일반적으로 *파티션 기준*을 사용하지 않는 쿼리에 대해 6 SU로 시작하는 것이 좋습니다. 그런 다음 대표적인 데이터 양을 전달하고 SU % 사용률 메트릭을 시험한 후 SU 수를 수정하는 평가판 및 오류 메서드를 사용하여 가장 적절한 부분을 판단합니다.

Azure Stream Analytics는 처리를 시작하기 전에 “다시 정렬 버퍼”라는 창에 이벤트를 둡니다. 이벤트는 다시 정렬 창에 시간별로 정렬되며 후속 작업은 임시로 정렬된 이벤트에서 수행됩니다. 시간으로 다시 정렬된 이벤트를 통해 연산자는 규정된 시간 내에 모든 이벤트에 대한 가시성이 확보됩니다. 또한 연산자는 필수 처리를 수행하고 출력을 생성할 수 있습니다. 이 메커니즘의 부작용은 다시 정렬 창의 기간에 의해 처리가 지연되는 것입니다. 작업의 메모리 공간(SU 사용에 영향을 줌)은 이 다시 정렬 창의 크기와 창에 포함된 이벤트 수의 함수입니다.

> [!NOTE]
> 작업을 업그레이드하는 동안 판독기 수가 변경되면 일시적인 경고가 감사 로그에 기록됩니다. Stream Analytics 작업은 자동으로 이러한 일시적인 문제를 복구합니다.

## <a name="common-high-memory-causes-for-high-su-usage-for-running-jobs"></a>높은 공용 메모리로 인해 실행 중인 작업에 높은 SU 사용량 유발

### <a name="high-cardinality-for-group-by"></a>그룹화 기준에 대한 높은 카디널리티

들어오는 이벤트의 카디널리티는 작업에 대한 메모리 사용량을 결정합니다.

예를 들어 `SELECT count(*) from input group by clustered, tumblingwindow (minutes, 5)`에서 **클러스터**와 연관된 번호는 쿼리의 카디널리티입니다.

높은 카디널리티에 의해 발생한 문제를 완화하려면 **파티션 기준**을 사용하여 파티션을 늘림으로써 쿼리의 규모를 확장합니다.

```
Select count(*) from input
Partition By clusterid
GROUP BY clustered tumblingwindow (minutes, 5)
```

*클러스터*의 수는 이 그룹화 기준의 카디널리티입니다.

쿼리가 분할되면 여러 노드에 걸쳐 분산됩니다. 결과적으로, 각 노드로 들어오는 이벤트 수가 감소하여 다시 정렬 버퍼의 크기가 줄어듭니다. 또한 partitionid에 따라 이벤트 허브 파티션을 분할해야 합니다.

### <a name="high-unmatched-event-count-for-join"></a>JOIN에 대해 일치하지 않는 이벤트 개수가 많음

JOIN에서 일치하지 않는 이벤트 수는 쿼리에 대한 메모리 사용률에 영향을 줍니다. 예를 들어 클릭을 유도하는 광고 노출 수를 찾는 쿼리를 수행합니다.

```
SELECT id from clicks INNER JOIN,
impressions on impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10
```

이 시나리오에서 많은 광고가 표시되고 몇 번의 클릭이 생성될 수 있습니다. 작업이 시간 창 내에서 모든 이벤트를 유지해야 그러한 결과가 도출됩니다. 사용된 메모리의 양은 창 크기 및 이벤트 속도에 비례합니다. 

이 상황을 완화하려면 파티션 기준을 사용하여 파티션을 늘려서 쿼리의 규모를 확장합니다. 

쿼리가 분할되면 여러 처리 노드에 걸쳐 분산됩니다. 결과적으로, 각 노드로 들어오는 이벤트 수가 감소하여 다시 정렬 버퍼의 크기가 줄어듭니다.

### <a name="large-number-of-out-of-order-events"></a>순서가 잘못된 이벤트 수가 많음 

큰 시간 창에 순서가 잘못된 이벤트가 많으면 “다시 정렬 버퍼”의 크기가 커집니다. 이 상황을 완화하려면 파티션 기준을 사용하여 파티션을 늘려서 쿼리의 규모를 조정합니다. 쿼리가 분할되면 여러 노드에 걸쳐 분산됩니다. 결과적으로, 각 노드로 들어오는 이벤트 수가 감소하여 다시 정렬 버퍼의 크기가 줄어듭니다. 


## <a name="get-help"></a>도움말 보기
추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

