---
title: 다시 분할을 사용 하 여 Azure Stream Analytics 작업 최적화
description: 이 문서에서는 분할을 사용 하 여 병렬화 할 수 없는 Azure Stream Analytics 작업을 최적화 하는 방법을 설명 합니다.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 95749f2acea6b605cfdba5a4f3d4f5526e751c5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182539"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>다시 분할을 사용 하 여 Azure Stream Analytics 처리 최적화

이 문서에서는 분할을 사용 하 여 완전히 [병렬화](stream-analytics-scale-jobs.md)할 수 없는 시나리오에 대 한 Azure Stream Analytics 쿼리 크기를 조정 하는 방법을 보여 줍니다.

다음 경우에는 [병렬화](stream-analytics-parallelization.md) 를 사용 하지 못할 수 있습니다.

* 입력 스트림에 대 한 파티션 키를 제어 하지 않습니다.
* 원본 "sprays" 입력은 나중에 병합 해야 하는 여러 파티션에 걸쳐 입력 합니다.

단지 섞는 Event Hubs에 대 한 **PartitionId** 와 같은 자연 입력 체계에 따라 분할 된 되지 않는 스트림에서 데이터를 처리 하는 경우에는 다시 분할 또는가 필요 합니다. 다시 분할 하면 각 분할 영역을 독립적으로 처리할 수 있으므로 스트리밍 파이프라인을 선형으로 확장할 수 있습니다. 

## <a name="how-to-repartition"></a>다시 분할 하는 방법
다음 두 가지 방법으로 입력을 다시 분할할 수 있습니다.
1. 분할을 수행 하는 별도의 Stream Analytics 작업을 사용 합니다.
2. 단일 작업을 사용 하지만 사용자 지정 분석 논리 전에 먼저 다시 분할을 수행 합니다.

### <a name="creating-a-separate-stream-analytics-job-to-repartition-input"></a>입력을 다시 분할 하는 별도의 Stream Analytics 작업 만들기
파티션 키를 사용 하 여 이벤트 허브 출력에 대 한 입력 및 쓰기를 읽는 작업을 만들 수 있습니다. 그런 다음이 이벤트 허브는 분석 논리를 구현 하는 다른 Stream Analytics 작업에 대 한 입력으로 사용할 수 있습니다. 작업에서이 이벤트 허브 출력을 구성 하는 경우 Stream Analytics에서 데이터를 다시 분할 하는 데 사용할 파티션 키를 지정 해야 합니다. 
```sql
-- For compat level 1.2 or higher
SELECT * 
INTO output
FROM input

--For compat level 1.1 or lower
SELECT *
INTO output
FROM input PARTITION BY PartitionId
```

### <a name="repartition-input-within-a-single-stream-analytics-job"></a>단일 Stream Analytics 작업 내에서 입력 다시 분할
먼저 입력을 다시 분할 한 다음 쿼리의 다른 단계에서 사용할 수 있는 단계를 쿼리에 도입할 수도 있습니다. 예를 들어 **DeviceId** 를 기준으로 입력을 다시 분할 하려는 경우 쿼리는 다음과 같습니다.
```sql
WITH RepartitionedInput AS 
( 
SELECT * 
FROM input PARTITION BY DeviceID
)

SELECT DeviceID, AVG(Reading) as AvgNormalReading  
INTO output
FROM RepartitionedInput  
GROUP BY DeviceId, TumblingWindow(minute, 1)  
```

다음 예제 쿼리는 다시 분할 데이터의 두 스트림을 조인 합니다. 다시 분할 데이터의 두 스트림을 조인 하는 경우 스트림은 동일한 파티션 키와 수를 가져야 합니다. 결과는 파티션 구성표가 동일한 스트림입니다.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

출력 체계는 각 하위 스트림이 독립적으로 플러시될 수 있도록 스트림 구성표 키 및 개수와 일치 해야 합니다. 스트림을 플러시하 기 전에 다른 스키마를 사용 하 여 다시 병합 하 고 다시 분할 수 있습니다. 그러나이 메서드는 처리의 일반 대기 시간에를 추가 하 고 리소스 사용률을 증가 시킬 수 있습니다.

## <a name="streaming-units-for-repartitions"></a>은의 스트리밍 단위

작업의 리소스 사용을 실험 하 고 관찰 하 여 필요한 파티션의 정확한 수를 확인 합니다. 각 파티션에 필요한 물리적 리소스에 따라 [SU (스트리밍 단위)](stream-analytics-streaming-unit-consumption.md) 수를 조정 해야 합니다. 일반적으로 각 파티션에는 6 개의 su가 필요 합니다. 작업에 할당 된 리소스가 부족 한 경우 시스템은 작업에 도움이 되는 경우에만 다시 분할을 적용 합니다.

## <a name="repartitions-for-sql-output"></a>SQL 출력의 repartitions

작업에서 출력에 SQL database를 사용 하는 경우 최적의 파티션 수와 일치 하도록 명시적 분할을 사용 하 여 처리량을 최대화 합니다. SQL은 8 개의 기록기에서 가장 잘 작동 하므로 플러시 전에 8로 흐름을 다시 분할 하거나 더 많은 업스트림에서 작업 성능을 향상 시킬 수 있습니다. 

입력 파티션이 8개보다 많은 경우 입력 파티션 구성표를 상속하는 것이 적합하지 않을 수 있습니다. 쿼리에서 [을](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 사용 하 여 출력 작성기 수를 명시적으로 지정 하는 것이 좋습니다. 

다음 예에서는 자연스럽 게 분할 되는 것에 관계 없이 입력을 읽고, DeviceID 차원에 따라 10 배나 스트림을 다시 분할 하 고, 데이터를 출력으로 플러시합니다. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

자세한 내용은 [Azure SQL Database에 Azure Stream Analytics 출력](stream-analytics-sql-output-perf.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 시작](stream-analytics-introduction.md)
* [Azure Stream Analytics에서 쿼리 병렬 처리 사용](stream-analytics-parallelization.md)
