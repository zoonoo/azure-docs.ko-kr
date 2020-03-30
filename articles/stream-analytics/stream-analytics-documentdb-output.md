---
title: Azure Cosmos DB에 Azure Stream Analytics 출력
description: 이 문서에서는 비구조화된 JSON 데이터에 대한 데이터 보관 및 짧은 대기 시간 쿼리를 위해 Azure Stream Analytics를 사용하여 JSON 출력에 대한 Azure Cosmos DB에 출력을 저장하는 방법을 알아봅니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: e58e36b3caa5a5ecd137cb9cb61dad7ddb95ff3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254444"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Cosmos DB에 Azure Stream Analytics 출력  
Azure Stream Analytics는 JSON 출력에 대한 [Azure Cosmos DB를](https://azure.microsoft.com/services/documentdb/) 대상으로 하여 비정형 JSON 데이터에 대한 데이터 보관 및 대기 시간 부족 쿼리를 활성화할 수 있습니다. 이 문서에서는 이 구성을 구현하기 위한 몇 가지 모범 사례를 설명합니다.

Azure Cosmos DB에 익숙하지 않은 경우 [Azure Cosmos DB 설명서를](https://docs.microsoft.com/azure/cosmos-db/) 참조하세요. 

> [!Note]
> 현재 스트림 애널리틱스는 *SQL API를*통해서만 Azure 코스모스 DB에 대한 연결을 지원합니다.
> 다른 Azure Cosmos DB API는 아직 지원되지 않습니다. 다른 API로 만든 Azure Cosmos DB 계정에 스트림 분석을 가리키면 데이터가 제대로 저장되지 않을 수 있습니다. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Azure 코스모스 DB의 기본 을 출력 대상으로
스트림 분석의 Azure Cosmos DB 출력을 사용하면 JSON출력을 Azure Cosmos DB 컨테이너에 출력할 때 스트림 처리 결과를 작성할 수 있습니다. 

스트림 애널리틱스는 데이터베이스에 컨테이너를 만들지 않습니다. 대신 앞에 만들어야 합니다. 그런 다음 Azure Cosmos DB 컨테이너의 청구 비용을 제어할 수 있습니다. [Azure Cosmos DB API를](https://msdn.microsoft.com/library/azure/dn781481.aspx)사용하여 컨테이너의 성능, 일관성 및 용량을 직접 조정할 수도 있습니다.

> [!Note]
> Azure Cosmos DB 방화벽에서 허용되는 IP 목록에 0.0.0.0을 추가해야 합니다.

다음 섹션에서는 Azure Cosmos DB에 대한 컨테이너 옵션 중 일부를 자세히 설명합니다.

## <a name="tuning-consistency-availability-and-latency"></a>일관성, 가용성 및 대기 시간 조정
응용 프로그램 요구 사항에 맞게 Azure Cosmos DB를 사용하면 데이터베이스와 컨테이너를 미세 조정하고 일관성, 가용성, 대기 시간 및 처리량 간에 절충을 할 수 있습니다. 

시나리오에서 읽기 및 쓰기 대기 시간에 대해 필요로 하는 읽기 일관성 수준에 따라 데이터베이스 계정에 대한 일관성 수준을 선택할 수 있습니다. 컨테이너에서 요청 단위(R)를 확장하여 처리량을 향상시킬 수 있습니다. 

또한 기본적으로 Azure Cosmos DB는 각 CRUD 작업에 대한 동기 인덱싱을 컨테이너에 사용할 수 있습니다. Azure Cosmos DB에서 쓰기/읽기 성능을 제어하는 또 다른 유용한 옵션입니다. 

자세한 내용은 데이터베이스 변경 및 쿼리 일관성 수준 문서를 [검토합니다.](../cosmos-db/consistency-levels.md)

## <a name="upserts-from-stream-analytics"></a>Stream Analytics에서 Upsert
Azure Cosmos DB와의 스트림 분석 통합을 사용하면 지정된 **문서 ID** 열을 기반으로 컨테이너에 레코드를 삽입하거나 업데이트할 수 있습니다. 이것은 또한 *upsert에게*불립니다.

스트림 애널리틱스는 낙관적인 업서트 접근 방식을 사용합니다. 업데이트는 문서 ID 충돌로 삽입에 실패하는 경우에만 발생합니다. 

호환성 수준 1.0을 사용하면 Stream Analytics에서 이 업데이트를 PATCH 작업으로 수행하므로 문서에 대한 부분 업데이트를 사용할 수 있습니다. 스트림 애널리틱스는 새 속성을 추가하거나 기존 속성을 증분 방식으로 대체합니다. 그러나 JSON 문서에서 배열 속성 값을 변경하면 전체 배열이 덮어쓰게 됩니다. 즉, 배열이 병합되지 않습니다. 

1.2에서는 upsert 동작이 수정되어 문서를 삽입하거나 교체합니다. 호환성 수준 1.2에 대한 이후 섹션에서는 이 동작에 대해 자세히 설명합니다.

들어오는 JSON 문서에 기존 ID 필드가 있는 경우 해당 필드는 Azure Cosmos DB의 **문서 ID** 열로 자동으로 사용됩니다. 후속 쓰기는 다음과 같이 처리되어 다음과 같은 상황 중 하나가 됩니다.

- 고유 한 아이디는 삽입으로 이어집니다.
- **ID로** 설정된 중복 ID 및 **문서 ID는** upsert로 이어집니다.
- 첫 번째 문서 후 ID와 **문서 ID를** 중복하여 설정하지 않은 경우 오류가 발생할 수 있습니다.

중복 ID가 있는 문서를 포함하여 *모든* 문서를 저장하려면 **AS** 키워드를 사용하여 쿼리의 ID 필드 이름을 바꿉니다. Azure Cosmos DB에서 ID 필드를 만들거나 **ID를 AS** 키워드를 사용하거나 **문서 ID** 설정을 사용하여 다른 열값으로 바꿉니다.

## <a name="data-partitioning-in-azure-cosmos-db"></a>Azure 코스모스 DB의 데이터 분할
Azure Cosmos DB는 워크로드에 따라 파티션을 자동으로 확장합니다. 따라서 데이터를 분할하기 위한 접근 법으로 [무제한](../cosmos-db/partition-data.md) 컨테이너를 권장합니다. Stream Analytics가 무제한 컨테이너에 쓸 때 이전 쿼리 단계 또는 입력 분할 구성표와 같은 많은 병렬 작성기를 사용합니다.

> [!NOTE]
> Azure Stream Analytics는 최상위 수준에서 파티션 키가 있는 무제한 컨테이너만 지원합니다. 예를 들어 `/region`이 지원됩니다. 중첩된 파티션 키(예: `/region/name`)는 지원되지 않습니다. 

파티션 키 선택에 따라 다음 _경고가_발생할 수 있습니다.

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

여러 가지 고유한 값을 가지며 이러한 값에 워크로드를 균등하게 분산할 수 있는 파티션 키 속성을 선택하는 것이 중요합니다. 분할의 자연스러운 아티팩트로서 동일한 파티션 키를 포함하는 요청은 단일 파티션의 최대 처리량에 의해 제한됩니다. 

동일한 파티션 키에 속하는 문서의 저장소 크기는 10GB로 제한됩니다. 이상적인 파티션 키는 쿼리에서 필터로 자주 나타나고 솔루션의 확장성을 보장하기에 충분한 카디널리티가 있는 키입니다.

파티션 키는 Azure Cosmos DB에 대한 저장 프로시저 및 트리거의 트랜잭션의 경계이기도 합니다. 트랜잭션에서 함께 발생하는 문서가 동일한 파티션 키 값을 공유하도록 파티션 키를 선택해야 합니다. Azure [Cosmos DB의 파티션](../cosmos-db/partitioning-overview.md) 문서에서파티션 키 선택에 대한 자세한 내용을 제공합니다.

고정된 Azure Cosmos DB 컨테이너의 경우 스트림 분석은 전체 가만히 있는 후에는 확장 또는 축소할 수 없습니다. 10GB 및 10,000 RU/s의 처리량의 상한이 있습니다. 고정 컨테이너에서 무제한 컨테이너(예: 1,000개 이상의 RU/s 및 파티션 키가 있는 컨테이너)로 데이터를 마이그레이션하려면 [데이터 마이그레이션 도구](../cosmos-db/import-data.md) 또는 변경 피드 [라이브러리를](../cosmos-db/change-feed.md)사용합니다.

여러 고정 컨테이너에 쓸 수 있는 기능이 더 이상 사용되지 않습니다. 스트림 애널리틱스 작업을 확장하는 것은 권장되지 않습니다.

## <a name="improved-throughput-with-compatibility-level-12"></a>호환성 수준 1.2로 처리량 향상
호환성 수준 1.2를 통해 Stream Analytics는 Azure Cosmos DB에 대량 쓰기에 대한 기본 통합을 지원합니다. 이러한 통합을 통해 Azure Cosmos DB에 효과적으로 쓸 수 있으며 처리량을 최대화하고 제한 요청을 효율적으로 처리할 수 있습니다. 

upsert 동작의 차이로 인해 향상된 쓰기 메커니즘을 새 호환성 수준에서 사용할 수 있습니다. 레벨이 1.2 이전인 경우 upsert 동작은 문서를 삽입하거나 병합하는 것입니다. 1.2에서는 upsert 동작이 수정되어 문서를 삽입하거나 교체합니다.

1.2 이전 수준으로 Stream Analytics는 사용자 지정 저장 프로시저를 사용하여 파티션 키당 문서를 Azure Cosmos DB로 일괄 처리합니다. 이 경우 일괄 처리는 트랜잭션으로 기록됩니다. 단일 레코드가 일시적인 오류(제한)에 도달하면 전체 일괄 처리를 다시 시도해야 합니다. 이렇게 하면 합리적인 제한이 있는 시나리오가 상대적으로 느려집니다.

다음 예제에서는 동일한 Azure 이벤트 허브 입력에서 읽는 두 개의 동일한 Stream Analytics 작업을 보여 주었습니다. 두 스트림 분석 작업은 통과 쿼리로 [완전히 분할되고](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) 동일한 Azure Cosmos DB 컨테이너에 씁니다. 왼쪽의 메트릭은 호환성 수준 1.0으로 구성된 작업에서 나온 것입니다. 오른쪽의 메트릭은 1.2로 구성됩니다. Azure Cosmos DB 컨테이너의 파티션 키는 입력 이벤트에서 오는 고유한 GUID입니다.

![스트림 분석 측정항목 비교](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

이벤트 허브의 들어오는 이벤트 속도는 Azure Cosmos DB 컨테이너(20,000R)보다 두 배 더 높므로 Azure Cosmos DB에서 제한이 예상됩니다. 그러나 1.2가 있는 작업은 처리량(분당 출력 이벤트)이 높고 평균 SU% 사용률이 낮아 일관되게 작성됩니다. 사용자 환경에서 이러한 차이는 몇 가지 요인에 따라 달라집니다. 이러한 요소에는 이벤트 형식, 입력 이벤트/메시지 크기, 파티션 키 및 쿼리 선택이 포함됩니다.

![Azure 코스모스 DB 메트릭 비교](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

1.2를 사용하면 Stream Analytics는 제한 또는 속도 제한에서 재제출이 거의 없는 Azure Cosmos DB에서 사용 가능한 처리량의 100%를 활용하는 데 보다 지능적입니다. 이렇게 하면 컨테이너에서 동시에 실행되는 쿼리와 같은 다른 워크로드에 더 나은 환경을 제공합니다. Stream Analytics가 Azure Cosmos DB를 초당 1,000~10,000개의 메시지싱크로 확장하는 방법을 보려면 [이 Azure 샘플 프로젝트를](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)사용해 보십시오.

Azure Cosmos DB 출력의 처리량은 1.0 및 1.1과 동일합니다. Azure Cosmos DB를 사용하여 스트림 분석에서 호환성 수준 1.2를 사용하는 *것이 좋습니다.*

## <a name="azure-cosmos-db-settings-for-json-output"></a>JSON 출력에 대한 Azure 코스모스 DB 설정

Azure Cosmos DB를 스트림 분석에서 출력으로 사용하면 다음과 같은 정보가 생성됩니다.

![Azure 코스모스 DB 출력 스트림에 대한 정보 필드](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|필드           | 설명|
|-------------   | -------------|
|출력 별칭    | 스트림 분석 쿼리에서 이 출력을 참조하는 별칭입니다.|
|Subscription    | Azure 구독입니다.|
|계정 ID      | Azure Cosmos DB 계정의 이름 또는 엔드포인트 URI입니다.|
|계정 키     | Azure Cosmos DB 계정에 대한 공유 액세스 키입니다.|
|데이터베이스        | Azure Cosmos DB 데이터베이스 이름입니다.|
|컨테이너 이름 | 컨테이너 이름(예: `MyContainer`.) 명명된 `MyContainer` 하나의 컨테이너가 있어야 합니다.  |
|문서 ID     | (선택 사항) 삽입 또는 업데이트 작업의 기준으로 사용해야 하는 고유 키로 사용되는 출력 이벤트의 열 이름입니다. 비워 두면 업데이트 옵션 없이 모든 이벤트가 삽입됩니다.|

Azure Cosmos DB 출력을 구성한 후 쿼리에서 [INTO 문의](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)대상으로 사용할 수 있습니다. 이러한 방식으로 Azure Cosmos DB 출력을 사용하는 경우 [파티션 키를 명시적으로 설정해야 합니다.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks) 

출력 레코드에는 Azure Cosmos DB의 파티션 키 의 이름을 따서 명명된 대/소문자를 구분하는 열이 포함되어야 합니다. 더 큰 병렬화를 달성하기 위해 문에는 동일한 열을 사용하는 [PARTITION BY 절이](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) 필요할 수 있습니다.

샘플 쿼리는 다음과 같습니다.

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>오류 처리 및 재시도

Stream Analytics에서 Azure Cosmos DB로 이벤트를 보내는 동안 일시적인 오류, 서비스 가용성 또는 제한이 발생하는 경우 스트림 애널리틱스는 작업을 성공적으로 완료하기 위해 무기한 으로 다시 시도합니다. 그러나 다음과 같은 오류에 대해 다시 시도하지 는 않습니다.

- 무단(HTTP 오류 코드 401)
- 찾을 수 없습니다(HTTP 오류 코드 404)
- 금지됨(HTTP 오류 코드 403)
- BadRequest(HTTP 오류 코드 400)
