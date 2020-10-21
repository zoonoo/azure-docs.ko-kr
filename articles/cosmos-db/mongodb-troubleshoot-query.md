---
title: MongoDB 용 Azure Cosmos DB API를 사용 하는 경우 쿼리 문제 해결
description: MongoDB 쿼리 문제에 대 한 Azure Cosmos DB의 API를 식별, 진단 및 해결 하는 방법에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/12/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 615bd423296fb9ed2ee28cab9e362873a30ee7b9
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284187"
---
# <a name="troubleshoot-query-issues-when-using-the-azure-cosmos-db-api-for-mongodb"></a>MongoDB 용 Azure Cosmos DB API를 사용 하는 경우 쿼리 문제 해결

이 문서에서는 Azure Cosmos DB의 쿼리 문제를 해결하기 위한 일반적인 권장 방법을 안내합니다. 이 문서에 설명된 단계가 잠재적 쿼리 문제에 대한 완전한 방어로 간주할 수는 없지만 여기에는 가장 일반적인 성능 팁이 포함되어 있습니다. MongoDB에 대 한 Azure Cosmos DB의 API에서 속도가 느리거나 비용이 많이 드는 쿼리 문제를 해결 하기 위한 시작 위치로이 문서를 사용 해야 합니다. Azure Cosmos DB core (SQL) API를 사용 하는 경우 [SQL api 쿼리 문제 해결 가이드](troubleshoot-query-performance.md) 문서를 참조 하세요.

Azure Cosmos DB의 쿼리 최적화는 광범위 하 게 다음과 같이 분류 됩니다.

- 쿼리의 RU(요청 단위) 비용을 낮추는 최적화
- 단지 대기 시간을 단축하는 최적화

쿼리 비용을 줄이는 경우 일반적으로 대기 시간도 줄일 수 있습니다.

이 문서에서는 [영양 데이터 집합](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)을 사용 하 여 다시 만들 수 있는 예제를 제공 합니다.

> [!NOTE] 
> 이 문서에서는 MongoDB에 대 한 Azure Cosmos DB "s API 버전 3.6을 사용 한다고 가정 합니다. 버전 3.2에서 성능이 저하 되는 일부 쿼리는 버전 3.6에서 크게 개선 되었습니다. [지원 요청](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 제출 하 여 버전 3.6으로 업그레이드 합니다.

## <a name="use-explain-command-to-get-metrics"></a>$explain 명령을 사용 하 여 메트릭 가져오기

Azure Cosmos DB에서 쿼리를 최적화 하는 경우 첫 번째 단계는 항상 쿼리에 대 한 프로그램 [요금을 확보](find-request-unit-charge-mongodb.md) 하는 것입니다. 대략적인 지침은 50 RUs 보다 큰 요금으로 쿼리를 위한 사용 요금을 낮추는 방법을 살펴보는 것입니다. 

명령을 사용 하 여 `$explain` 쿼리 및 인덱스 사용 메트릭을 가져올 수 있습니다. 다음은 쿼리를 실행 하 고 명령을 사용 하 여 `$explain` 쿼리 및 인덱스 사용 메트릭을 표시 하는 예입니다.

**명령 $explain:**

```
db.coll.find({foodGroup: "Baby Foods"}).explain({"executionStatistics": true })
```

**출력:**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 905.2888,
            "timeExclusiveMS" : 905.2888,
            "in" : 362,
            "out" : 362,
            "details" : {
                "database" : "db-test",
                "collection" : "collection-test",
                "query" : {
                    "foodGroup" : {
                        "$eq" : "Baby Foods"
                    }
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "e68e6bdd-5e89-4ec5-b053-3dbbc2428140",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 788.5867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 362,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 362,
                    "outputDocumentSizeBytes" : 2553535,
                    "indexHitRatio" : 0.0016802042237178,
                    "totalQueryExecutionTimeMS" : 777.72,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.19,
                        "logicalPlanBuildTimeMS" : 0.14,
                        "physicalPlanBuildTimeMS" : 0.09,
                        "queryOptimizationTimeMS" : 0.03
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 687.22,
                    "vmExecutionTimeMS" : 774.09,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 37.45,
                        "systemFunctionExecutionTimeMS" : 10.82,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 49.42
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

`$explain`명령 출력은 길고 쿼리 실행에 대 한 자세한 정보를 포함 합니다. 일반적으로 쿼리 성능을 최적화할 때 중점적으로 확인할 수 있는 몇 가지 섹션이 있습니다.

| 메트릭 | 설명 | 
| ------ | ----------- |
| `timeInclusiveMS` | 백 엔드 쿼리 대기 시간 |
| `pathsIndexed` | 쿼리에서 사용한 인덱스를 표시 합니다. | 
| `pathsNotIndexed` | 쿼리에서 사용할 수 있는 인덱스를 표시 합니다 (사용 가능한 경우). | 
| `shardInformation` | 특정 [물리적 파티션의](partition-data.md#physical-partitions) 쿼리 성능 요약 | 
| `retrievedDocumentCount` | 쿼리 엔진에서 로드 한 문서 수 | 
| `outputDocumentCount` | 쿼리 결과에 반환 된 문서 수 | 
| `estimatedDelayFromRateLimitingInMilliseconds` | 요금 제한으로 인 한 추가 쿼리 대기 시간 | 

쿼리 메트릭을 가져온 후에는 `retrievedDocumentCount` 와 쿼리를 비교 합니다 `outputDocumentCount` . 이 비교를 사용하여 이 문서에서 검토할 관련 섹션을 식별할 수 있습니다. 는 `retrievedDocumentCount`  쿼리 엔진에서 로드 해야 하는 문서 수입니다. 는 `outputDocumentCount` 쿼리 결과에 필요한 문서 수입니다. `retrievedDocumentCount`가 보다 큰 경우 `outputDocumentCount` 인덱스를 사용할 수 없고 검색을 수행 하는 데 필요한 쿼리의 일부가 하나 이상 있습니다.

시나리오와 관련된 쿼리 최적화를 이해하려면 다음 섹션을 참조하십시오.

### <a name="querys-ru-charge-is-too-high"></a>쿼리의 RU 요금이 너무 높음

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>검색된 문서 수가 출력 문서 수보다 크게 많음

- [필요한 인덱스를 포함 합니다.](#include-necessary-indexes)

- [인덱스를 사용 하는 집계 작업을 파악 합니다.](#understand-which-aggregation-operations-use-the-index)

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>검색된 문서 수와 출력 문서 수가 거의 동일함

- [파티션 간 쿼리를 최소화합니다.](#minimize-cross-partition-queries)

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>쿼리의 RU 요금은 감수할만한 수준이지만 대기 시간이 너무 높음

- [근접성을 개선합니다.](#improve-proximity)

- [프로비전된 처리량을 늘립니다.](#increase-provisioned-throughput)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>검색 된 문서 개수가 출력 문서 수를 초과 하는 쿼리

 는 `retrievedDocumentCount` 쿼리 엔진에서 로드 하는 데 필요한 문서 수입니다. 는 `outputDocumentCount` 쿼리에서 반환 된 문서 수입니다. `retrievedDocumentCount`가 보다 큰 경우 `outputDocumentCount` 인덱스를 사용할 수 없고 검색을 수행 하는 데 필요한 쿼리의 일부가 하나 이상 있습니다.

다음은 인덱스에 의해 완전히 처리되지 않은 검색 쿼리의 예입니다.

**명령 $explain:**

```
db.coll.find(
  {
    $and : [
            { "foodGroup" : "Cereal Grains and Pasta"}, 
            { "description" : "Oat bran, cooked"}
        ]
  }
).explain({"executionStatistics": true })
```

**출력:**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 436.5716,
            "timeExclusiveMS" : 436.5716,
            "in" : 1,
            "out" : 1,
            "details" : {
                "database" : "db-test",
                "collection" : "indexing-test",
                "query" : {
                    "$and" : [ 
                        {
                            "foodGroup" : {
                                "$eq" : "Cereal Grains and Pasta"
                            }
                        }, 
                        {
                            "description" : {
                                "$eq" : "Oat bran, cooked"
                            }
                        }
                    ]
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "13a5977e-a10a-4329-b68e-87e4f0081cac",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 435.4867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 1,
                    "outputDocumentSizeBytes" : 6064,
                    "indexHitRatio" : 0.0,
                    "totalQueryExecutionTimeMS" : 433.64,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.12,
                        "logicalPlanBuildTimeMS" : 0.09,
                        "physicalPlanBuildTimeMS" : 0.1,
                        "queryOptimizationTimeMS" : 0.02
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 387.44,
                    "vmExecutionTimeMS" : 432.93,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 45.36,
                        "systemFunctionExecutionTimeMS" : 16.86,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 0.13
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

`retrievedDocumentCount`(8618)는 (1) 보다 훨씬 더 높습니다 `outputDocumentCount` .이 쿼리에는 문서 검색이 필요 합니다. 

### <a name="include-necessary-indexes"></a>필요한 인덱스 포함

`pathsNotIndexed`배열을 확인 하 고 이러한 인덱스를 추가 해야 합니다. 이 예제에서는 및 경로를 `foodGroup` `description` 인덱싱해야 합니다.

```json
"pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ]
```

MongoDB에 대 한 Azure Cosmos DB API의 인덱싱 모범 사례는 MongoDB와 다릅니다. MongoDB에 대 한 Azure Cosmos DB API에서 복합 인덱스는 여러 속성을 효율적으로 정렬 해야 하는 쿼리에만 사용 됩니다. 여러 속성에 대 한 필터가 있는 쿼리를 사용 하는 경우 이러한 각 속성에 대해 단일 필드 인덱스를 만들어야 합니다. 쿼리 조건자는 여러 개의 단일 필드 인덱스를 사용할 수 있습니다.

[와일드 카드 인덱스](mongodb-indexing.md#wildcard-indexes) 는 인덱싱을 단순화할 수 있습니다. MongoDB와 달리 와일드 카드 인덱스는 쿼리 조건자의 여러 필드를 지원할 수 있습니다. 각 속성에 대 한 별도의 인덱스를 만드는 대신 하나의 와일드 카드 인덱스를 사용 하는 경우 쿼리 성능에 차이가 없습니다. 모든 속성에 대해 와일드 카드 인덱스를 추가 하는 것이 모든 쿼리를 최적화 하는 가장 쉬운 방법입니다.

쓰기 또는 읽기 가용성에 영향을 주지 않고 언제 든 지 새 인덱스를 추가할 수 있습니다. [인덱스 변환 진행률을 추적](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)할 수 있습니다.

### <a name="understand-which-aggregation-operations-use-the-index"></a>인덱스를 사용 하는 집계 작업 이해

대부분의 경우 MongoDB에 대 한 Azure Cosmos DB API의 집계 작업은 인덱스를 부분적으로 사용 합니다. 일반적으로 쿼리 엔진은 같음 및 범위 필터를 먼저 적용 하 고 인덱스를 사용 합니다. 쿼리 엔진은 이러한 필터를 적용한 후 추가 필터를 평가하고 필요한 경우 집계를 계산하기 위해 나머지 문서를 로드할 수 있습니다. 

예는 다음과 같습니다.

```
db.coll.aggregate( [
   { $match: { foodGroup: 'Fruits and Fruit Juices' } },
   {
     $group: {
        _id: "$foodGroup",
        total: { $max: "$version" }
     }
   }
] )
```

이 경우 인덱스는 단계를 최적화할 수 있습니다 `$match` . 에 대 한 인덱스를 추가 `foodGroup` 하면 쿼리 성능이 크게 향상 됩니다. MongoDB에서와 마찬가지로 `$match` 인덱스 사용을 최대화 하기 위해 가능한 한 집계 파이프라인의 초기에를 넣어야 합니다.

MongoDB에 대 한 Azure Cosmos DB API에서 인덱스는 실제 집계 (이 경우)에는 사용 되지 않습니다 `$max` . 에 인덱스를 추가 `version` 하면 쿼리 성능이 향상 되지 않습니다.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>검색 된 문서 수가 출력 문서 수와 같은 쿼리

가와 `retrievedDocumentCount` 거의 같은 경우 `outputDocumentCount` 쿼리 엔진은 불필요 한 많은 문서를 검색할 필요가 없습니다.

### <a name="minimize-cross-partition-queries"></a>파티션 간 쿼리를 최소화

Azure Cosmos DB는 요청 단위 및 데이터 스토리지 증가가 필요한 경우 [분할](partitioning-overview.md)을 사용하여 개별 컨테이너를 확장합니다. 각 실제 파티션에는 별도의 독립된 인덱스가 있습니다. 쿼리에 컨테이너의 파티션 키와 일치하는 같음 필터가 있는 경우 관련 파티션의 인덱스만 확인하면 됩니다. 이러한 최적화를 통해 쿼리에 필요한 총 RU 수가 줄어듭니다. [파티션 내 쿼리와 파티션 간 쿼리 간의 차이점에 대해 자세히 알아보세요](how-to-query-container.md).

프로비전된 RU(30,000개 초과)가 많거나 대용량의 데이터가 저장되어 있는 경우(약 100GB 초과) 컨테이너 크기가 충분히 크다면 쿼리 RU 비용을 크게 줄일 수 있습니다. 

배열을 확인 하 여 `shardInformation` 각 개별 실제 파티션에 대 한 쿼리 메트릭을 이해할 수 있습니다. 고유 값 수는 `shardKeyRangeId` 쿼리를 실행 하는 데 필요한 실제 파티션 수입니다. 이 예에서는 4 개의 실제 파티션에서 쿼리가 실행 되었습니다. 실행은 인덱스 사용량과 완전히 독립적 이라고 이해 하는 것이 중요 합니다. 즉, 파티션 간 쿼리는 인덱스를 계속 사용할 수 있습니다.

```json
  "shardInformation" : [ 
                    {
                        "activityId" : "42f670a8-a201-4c58-8023-363ac18d9e18",
                        "shardKeyRangeId" : "5",
                        "durationMS" : 24.3859,
                        "preemptions" : 1,
                        "outputDocumentCount" : 463,
                        "retrievedDocumentCount" : 463
                    }, 
                    {
                        "activityId" : "a8bf762a-37b9-4c07-8ed4-ae49961373c0",
                        "shardKeyRangeId" : "2",
                        "durationMS" : 35.8328,
                        "preemptions" : 1,
                        "outputDocumentCount" : 905,
                        "retrievedDocumentCount" : 905
                    }, 
                    {
                        "activityId" : "3754e36b-4258-49a6-8d4d-010555628395",
                        "shardKeyRangeId" : "1",
                        "durationMS" : 67.3969,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1479,
                        "retrievedDocumentCount" : 1479
                    }, 
                    {
                        "activityId" : "a69a44ee-db97-4fe9-b489-3791f3d52878",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 185.1523,
                        "preemptions" : 1,
                        "outputDocumentCount" : 867,
                        "retrievedDocumentCount" : 867
                    }
                ]
```

## <a name="optimizations-that-reduce-query-latency"></a>쿼리 대기 시간을 줄이는 최적화

많은 경우에 RU 요금은 감수할만한 수준이지만 쿼리 대기 시간은 매우 높을 수 있습니다. 다음 섹션에서는 쿼리 대기 시간을 줄이기 위한 팁을 개략적으로 설명합니다. 동일한 쿼리를 동일한 데이터 집합에서 여러 번 실행 하는 경우에는 일반적으로 매번 동일한 실행 요금이 발생 합니다. 하지만 쿼리 대기 시간은 쿼리 실행마다 다를 수 있습니다.

### <a name="improve-proximity"></a>근접성 향상

Azure Cosmos DB 계정이 아닌 다른 지역에서 실행되는 쿼리는 동일한 지역 내에서 실행된 경우 보다 대기 시간이 더 높습니다. 예를 들어 데스크톱 컴퓨터에서 코드를 실행하는 경우 쿼리가 Azure Cosmos DB와 동일한 Azure 지역 내의 가상 머신에서 나온 것보다 수십 또는 수백 밀리초(또는 그 이상) 높은 대기 시간을 예상해야 합니다. 데이터를 앱에 더 가깝게 가져올 수 있도록 [Azure Cosmos DB 데이터를 전 세계적으로 배포](tutorial-global-distribution-mongodb.md)하는 것은 간단합니다.

### <a name="increase-provisioned-throughput"></a>프로비전된 처리량 증가

Azure Cosmos DB에서 프로비전된 처리량은 RU(요청 단위)로 측정됩니다. 5RU의 처리량을 사용하는 쿼리가 있다고 가정합니다. 예를 들어 1,000RU를 프로비전하는 경우 해당 쿼리를 초당 200번 실행할 수 있습니다. 사용할 수 있는 처리량이 충분 하지 않은 경우 쿼리를 실행 하려고 하면 Azure Cosmos DB에서 요청을 제한 하 게 됩니다. MongoDB에 대 한 Azure Cosmos DB API는 짧은 시간 동안 기다린 후이 쿼리를 자동으로 다시 시도 합니다. 제한된 요청은 시간이 오래 걸리므로 프로비전된 처리량을 늘리면 쿼리 대기 시간이 향상될 수 있습니다.

이 값은 `estimatedDelayFromRateLimitingInMilliseconds` 처리량을 늘리면 잠재적 대기 시간 혜택을 의미 합니다.

## <a name="next-steps"></a>다음 단계

* [쿼리 성능 문제 해결 (SQL API)](troubleshoot-query-performance.md)
* [MongoDB에 대 한 Azure Cosmos DB의 API에서 인덱싱 관리](mongodb-indexing.md)
