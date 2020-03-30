---
title: 재분할을 사용하여 Azure 스트림 분석 작업 최적화
description: 이 문서에서는 재분할을 사용하여 병렬화할 수 없는 Azure Stream Analytics 작업을 최적화하는 방법을 설명합니다.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c70cfb6c1626908a2ba4e707a890f6dc7481c51a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732385"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>다시 분할을 사용하여 Azure Stream Analytics로 처리를 최적화

이 문서에서는 완전히 [병렬화할](stream-analytics-scale-jobs.md)수 없는 시나리오에 대해 Azure Stream Analytics 쿼리를 확장하기 위해 재분할을 사용하는 방법을 보여 줍니다.

다음과 같은 경우 [병렬화를](stream-analytics-parallelization.md) 사용하지 못할 수 있습니다.

* 입력 스트림의 파티션 키를 제어하지 않습니다.
* 소스는 나중에 병합해야 하는 여러 파티션에 "스프레이"를 입력합니다.

이벤트 허브용 **PartitionId와** 같은 자연스러운 입력 체계에 따라 샤드되지 않은 스트림에서 데이터를 처리할 때 다시 분할하거나 재정의해야 합니다. 다시 분할할 때 각 샤드를 독립적으로 처리할 수 있으므로 스트리밍 파이프라인을 선형으로 확장할 수 있습니다.

## <a name="how-to-repartition"></a>다시 파티션하는 방법

다시 분할하려면 쿼리에서 **파티션 BY** 문 **후** INTO 키워드를 사용합니다. 다음 예제는 **DeviceID별** 데이터를 파티션 수 10으로 분할합니다. **DeviceID의** 해시는 어떤 파티션이 어떤 하위 스트림을 수락할지 결정하는 데 사용됩니다. 데이터는 분할된 각 스트림에 대해 독립적으로 플러시되며, 출력이 분할된 쓰기를 지원하고 10개의 파티션이 있다고 가정합니다.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

다음 예제 쿼리는 다시 분할된 데이터의 두 스트림을 조인합니다. 다시 분할된 데이터의 두 스트림을 조인할 때 스트림에는 동일한 파티션 키와 개수가 있어야 합니다. 결과는 동일한 파티션 구성표를 가지는 스트림입니다.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

출력 구성표는 각 하위 스트림을 독립적으로 플러시할 수 있도록 스트림 구성표 키와 카운트와 일치해야 합니다. 플러시 하기 전에 다른 구성표에 의해 스트림병합 및 다시 분할 될 수 있습니다 하지만 처리의 일반적인 대기 시간에 추가 하 고 리소스 사용률을 증가 하기 때문에 이 메서드를 피해 야 합니다.

## <a name="streaming-units-for-repartitions"></a>재파티션을 위한 스트리밍 유닛

작업의 리소스 사용량을 실험하고 관찰하여 필요한 파티션의 정확한 수를 확인합니다. 스트리밍 [단위(SU)의](stream-analytics-streaming-unit-consumption.md) 수는 각 파티션에 필요한 물리적 리소스에 따라 조정되어야 합니다. 일반적으로 각 파티션에 대해 6개의 US가 필요합니다. 작업에 할당된 리소스가 부족한 경우 시스템은 작업에 이점을 줄 경우에만 재분할을 적용합니다.

## <a name="repartitions-for-sql-output"></a>SQL 출력을 위한 재분할

작업이 SQL 데이터베이스를 출력에 사용하는 경우 명시적 재분할을 사용하여 최적의 파티션 수와 일치하여 처리량을 최대화합니다. SQL은 8명의 작성자와 함께 가장 잘 작동하므로 플러시하기 전에 8개 또는 업스트림 에서 더 많은 스트림으로 흐름을 다시 분할하면 작업 성능에 도움이 될 수 있습니다. 

입력 파티션이 8개보다 많은 경우 입력 파티션 구성표를 상속하는 것이 적합하지 않을 수 있습니다. 쿼리에서 [INTO를](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 사용하여 출력 작성자 수를 명시적으로 지정하는 것이 좋습니다. 

다음 예제에서는 입력이 자연적으로 분할되는 것에 관계없이 입력에서 읽고 DeviceID 차원에 따라 스트림을 10배 다시 분할하고 데이터를 출력으로 플러시합니다. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

자세한 내용은 [Azure SQL Database에 Azure Stream Analytics 출력](stream-analytics-sql-output-perf.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 시작](stream-analytics-introduction.md)
* [Azure Stream Analytics에서 쿼리 병렬 처리 사용](stream-analytics-parallelization.md)
