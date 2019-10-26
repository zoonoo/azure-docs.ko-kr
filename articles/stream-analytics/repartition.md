---
title: 다시 분할을 사용 하 여 Azure Stream Analytics 작업 최적화
description: 이 문서에서는 분할을 사용 하 여 병렬화 할 수 없는 Azure Stream Analytics 작업을 최적화 하는 방법을 설명 합니다.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: bbea71464e8a1f4e93e510106d372257f155b0c6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935059"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>다시 분할을 사용 하 여 Azure Stream Analytics 처리 최적화

이 문서에서는 분할을 사용 하 여 완전히 [병렬화](stream-analytics-scale-jobs.md)할 수 없는 시나리오에 대 한 Azure Stream Analytics 쿼리 크기를 조정 하는 방법을 보여 줍니다.

다음 경우에는 [병렬화](stream-analytics-parallelization.md) 를 사용 하지 못할 수 있습니다.

* 입력 스트림에 대 한 파티션 키를 제어 하지 않습니다.
* 원본 "sprays" 입력은 나중에 병합 해야 하는 여러 파티션에 걸쳐 입력 합니다. 

## <a name="how-to-repartition"></a>다시 분할 하는 방법

단지 섞는 Event Hubs에 대 한 **PartitionId** 와 같은 자연 입력 체계에 따라 분할 된 되지 않는 스트림에서 데이터를 처리 하는 경우에는 다시 분할 또는가 필요 합니다. 다시 분할 하면 각 분할 영역을 독립적으로 처리할 수 있으므로 스트리밍 파이프라인을 선형으로 확장할 수 있습니다.

다시 분할 하려면 쿼리의 **PARTITION BY** 문 뒤 **에 키워드를** 사용 합니다. 다음 예에서는 **DeviceID** 로 데이터를 파티션 수 10으로 분할 합니다. **DeviceID** 의 해시를 사용 하 여 어떤 파티션이 어떤 파티션을 허용 해야 하는지 결정 합니다. 데이터가 분할 된 쓰기를 지원 하 고 10 개의 파티션이 있는 경우 분할 된 각 스트림에 대해 독립적으로 데이터가 플러시됩니다.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

다음 예제 쿼리는 다시 분할 데이터의 두 스트림을 조인 합니다. 다시 분할 데이터의 두 스트림을 조인 하는 경우 스트림은 동일한 파티션 키와 수를 가져야 합니다. 결과는 파티션 구성표가 동일한 스트림입니다.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

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
* [Azure Stream Analytics에서 쿼리 병렬화 활용](stream-analytics-parallelization.md)
